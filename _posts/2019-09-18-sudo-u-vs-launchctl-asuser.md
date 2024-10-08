## sudo -u vs launchctl asuser

_Update: Scripting OS X has an excellent post on this topic over on his blog - well worth the read https://scriptingosx.com/2020/08/running-a-command-as-another-user/_

The Other Day I was troubleshooting an issue I was having with a jamf policy that was designed to send a notification to a group of computers. I scoped it to a group of computers, set it to run once a month and waited to see how the deployment progressed.

Nothing happened and the number complete in the policy status didn't increase even after an hour or more (other than my two test machines)

![](https://breardon.home.blog/wp-content/uploads/2019/09/image.png?w=183)

Troubleshooting the issue I discovered there was a problem with a notification service I had deployed (a custom fork of the excellent yo https://github.com/sheagcraig/yo ). The policy was running but the notifications were not being sent. There was an error in the logs to the effect that there was no display and so the entire `jamf policy` run was being held up by hours until the process eventually timed out or the machine was rebooted - nothing was running. This was strange as I had set up the script to send the notifications as the current user using `sudo -u username ...` and everything worked well in testing.

It all worked fine if called from a self service policy or if triggered with a `sudo jamf policy -trigger ...` on the command line and so did not present as an issue during testing. What I had failed to test is when the policy run is launched every 15 minutes via launchd as directed in `/Library/LaunchDaemons/com.jamfsoftware.task.1.plist`

In this context there was no display for the process to access, and so running a command via `sudo -u` would also not have a display, hence the notification service hanging.

`launchctl asuser`, as per the man page, "...executes the given command in as similar an execution context as possible to that of the target user's bootstrap". It requires UID instead of username as the parameter. If you get the current logged in user with either `stat -f%Su /dev/console` or with the `$3` parameter that jamf passes into all scripts (or <insert favourite method here>), you can then get UID with a simple`id -u "$loggedInUser"`.

For running a command you could do:

```
launchctl asuser <uid> command
```

launchctl will then execute the command in the users context, including access to the display for programs that have a UI component, even when launched via a system launch daemon. 👍

For loading and unloading launch agents you would so something like:

```
launchctl asuser <uid> launchctl load /Library/LaunchAgents/com.some.agent.plist
```

`sudo -u` has it's place but if you're wanting to ensure to are running in the users context, `launchctl asuser` seems to be the better fit.

(not mentioned here - all the code and checks to make sure there's a user to send a notification to in the first place - usually a good thing to do and be aware of)

no client workstations were harmed in the production of this post

@bartreardon on macadmins slack, twitter and instagram
