---
layout: post
title: "Playing Nice"
date: 2025-04-23
---

![Nice Agents](/images/nice-agents.png)

I've written about issues with [deploying too many system agents](https://bartreardon.github.io/2022/05/19/on-the-topic-of-deploying-a-multitude-of-system-agents.html) before but in a managed environment some things are unavoidable. We can do some things to make them better behaved though, and ensure that usability of the system by the human at the keyboard is prioritised over other processes.

<!--more-->

The main issue is that a lot of agents install with default performance settings and as such, will compete for system resources on equal footing with user apps and other processes.

A greedy system agent might want to wake up on a certain schedule and consume as much CPU as it can before going back to sleep. While running, CPU usage can spike, the system can become sluggish and the user experience can become degraded.

As a sysadmin, we want to balance system performance so we can meet any security or management requirements while minimising the impact to the end user trying to actually do work.


## What is Nice

Nice, in BSD terms, is a property that informs the OS how "nice" the process should be to other processes on the system when it comes to system resources like CPU usage. This can range from -20 to +20. If unset, the default is 0.

https://www.manpagez.com/man/3/nice/ 

The higher the value, the "more nice" a process can be with regard to giving up resources to higher priority tasks. Given a limited resource, macOS will prioritise processes with lower (i.e. less "nice") nice values.

Note that this does **_not_** limit the resources a process can use, only how the process is scheduled by the OS. If a process wants to use 100% CPU and 100% of CPU is available then the OS will allocate it. If a higher priority task comes along though then resources will be re-allocated as required. 

From a sysadmin perspective, this means you can set the nice property on running system agents and let the OS manage the resources, so for example if your video conference app has a higher priority than your inventory collection daemon, the latter won't interfere as much as it would if they both had the same priority, but if the system is idle, the system agent will be able to ramp up and complete its tasks.

## Making a process behave nicely

launchd has a property in the agent/daemon plist that can set this:

```xml
<key>Nice</key>
<integer>5</integer>
```

...however most of the time you don't want to be maintaining this directly for third party launchd plists (although you can if that's what you really want to do). Instead we can use the `renice` command to set the nice property of an existing process like so:

```bash
pid=$(pgrep -f "$PROCESS_NAME")
renice +20 -p "$pid"
```

This will set the nice property of the identified process to the highest value and nearly every other process on the system will take precedence. If you have particularly resource hungry processes, this can improve user experience in the UI quite a bit, while letting the system agent do its thing. The nice value will also applies to any child processes the agent might spawn.

You can validate the nice value of a running process by running:

```bash
ps -o ni= -p "$pid"
```

**Disclaimer:** Running `renice` on third party software should have no issues but you should verify that doing so doesn't have any adverse effects on how the software needs to operate. Testing and validation is very much recommended.

_The last time `renice` was mentioned on the [MacAdmins Slack](https://www.macadmins.org) was in 2019 so I figured it was worth blogging about._

## Limiting CPU resources

While we can't set CPU resources directly, like "use 10% CPU only" we can limit the process on Apple Silicon macs to run on efficiency cores only. Howard Oakley has a great writeup on that on the [Eclectic Light Company blog](https://eclecticlight.co/2022/10/20/making-the-most-of-apple-silicon-power-5-user-control/)

To do this we can use the `taskpolicy` command like so:

```bash
taskpolicy -b -p "$pid"
```

This will limit the process to running on efficiency cores only, further improving resource utalisation for low priority tasks.

## Tying things together

Here is a script that can perform all the above. It takes a process name as an argument and optionally a nice value in the range of -20 to +20. ([link](https://github.com/bartreardon/macscripts/blob/master/renice-process.sh))

This would need to run after the system has booted and agents are running. An option would be to use this with an [Outset](https://github.com/macadmins/outset) `login-privileged-every` task, but could also run from an MDM or other device management framework automatically or on request.

```bash
#!/bin/bash

# Script to renice processes.

# Author: Bart Reardon
# Date: 2025-04-24
# Version: 1.0

## Description:
# This sets the priority of processes to the specified value.
# It will check for the process every 60 seconds and renice it if found.
# If the process is not found, it will retry for a maximum of 10 times.
# If the process is not found after 10 retries, it will exit with an error.
# Log output is written to /var/log/renice-<process_name>.log

# Usage: ./renice-process.sh <process_name> [nice_value]
# Example: ./renice-process.sh 'MyProcess' +10

# Process name will match any process name that contains the string.
# For example, if the process name is 'MyProcess', it will match 'MyProcess', 'MyProcess2', 'MyProcess3', etc.


PROCESS_NAME="$1"
NICE_VALUE="${2:-+10}" # Default to +20 if not provided
# If using in a jamf script:
# PROCESS_NAME="$4"
# NICE_VALUE="${5:-+10}" # Default to +20 if not provided

# Constants
RETRY_INTERVAL=60 # seconds
MAX_RETRIES=10 # maximum number of retries
TOTAL_RUNTIME=600 # seconds (10 minutes)
START_TIME=$(date +%s)

print_usage() {
    echo "Usage: $0 <process_name> [nice_value]"
    echo "Example: $0 'MyProcess' +10"
}

# check if running as root
if [ "$(id -u)" -ne 0 ]; then
    echo "This script must be run as root. Please run with sudo."
    exit 1
fi
# check PROCESS_NAME is not empty
if [ -z "$PROCESS_NAME" ]; then
    echo "Process name is required."
    print_usage
    exit 1
fi
# NICE_VALUE needs to be in the range -20 to +20
if ! [[ "$NICE_VALUE" =~ ^[-+]?[0-9]+$ ]] || [ "$NICE_VALUE" -lt -20 ] || [ "$NICE_VALUE" -gt 20 ]; then
    echo "nice_value must be an integer between -20 and +20."
    print_usage
    exit 1
fi

# log file
LOG_FILE="/var/log/renice-${PROCESS_NAME}.log"
# Create log directory if it doesn't exist
mkdir -p "$(dirname "$LOG_FILE")"

write_log() {
  local message="$1"
  # echo to stdout
  echo "$message"
  # echo to log file
  echo "$(date +'%Y-%m-%d %H:%M:%S') - $message" >> "$LOG_FILE"
}

# Function to renice  processes
renice_process() {
    pids=$(pgrep -f "$PROCESS_NAME")

    if [ -n "$pids" ]; then
        # echo to stderr to avoid confusion with stdout
        write_log "Found one or more processes for ${PROCESS_NAME}:"
        for pid in $pids; do
            write_log "Evaluating $(get_process_name "$pid") with PID $pid"
            # Check if the process is already reniced to the desired value
            if [[ $(get_nice_value "$pid") -eq "$NICE_VALUE" ]]; then
                write_log "Process $pid already has nice value of ${NICE_VALUE}. Skipping..."
                continue
            fi
            /usr/bin/renice ${NICE_VALUE} -p "$pid"
            /usr/sbin/taskpolicy -b -p "$pid"
            write_log "Reniced process $pid to ${NICE_VALUE} priority and limit to E cores only"
        done
        return 0
    else
        return 1
    fi
}

# Return the current nice value of a given PID
get_nice_value() {
    ps -o ni= -p "$1"
}

# Return process name of a given PID
get_process_name() {
    ps -o comm= -p "$1"
}

# Main loop
retry_count=0
write_log "Starting renice script..."
while true; do
    CURRENT_TIME=$(date +%s)
    ELAPSED_TIME=$((CURRENT_TIME - START_TIME))

    if [ "$ELAPSED_TIME" -ge "$TOTAL_RUNTIME" ]; then
        echo "Maximum runtime (10 minutes) exceeded.  Exiting."
        exit 1
    fi

    renice_process
    return_code=$?

    if [[ $return_code == 0 ]]; then
        echo "${PROCESS_NAME} process reniced successfully."
        break
    else
        echo "${PROCESS_NAME} process not found. Retrying in $RETRY_INTERVAL seconds..."
        sleep "$RETRY_INTERVAL"
        retry_count=$((retry_count + 1))

        if [ "$retry_count" -ge "$MAX_RETRIES" ]; then
            echo "Maximum retries reached. Exiting."
            exit 1
        fi
    fi
done
```

## Extra Credit

If we were talking about a Linux system using systemd, we can be quite explicit about setting process priority and resource limitations.

For example for a systemd process `foo` we can create an override that is persistant and sets the values we want:

```bash
sudo systemctl edit foo
```

will create `/etc/systemd/system/foo.service.d/override.conf`

In that we can say something like:

```ini
[Service]
CPUWeight=5
CPUQuota=10%
```

And the process, and child tasks, will be hard limited to 10% utilisation.

If only this type of override were possible in launchd, but unfortunatly for us, it's not 🙂 (that I'm aware of - very happy to be corrected)
