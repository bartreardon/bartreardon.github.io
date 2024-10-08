## Discovering non-native macOS Applications

As was announced at WWDC 2020, Apple will be releasing Macs later this year running on Apple Silicon based on the ARM64 architecture. This transition will hopefully have us running universal applications but also possibly forced to run some intel only apps transcoded through Rosetta 2, depending on vendor support. As a mac admin it might be handy to know how to discover what applications on your systems don't have a version compiled for ARM (or intel 64 bit for that matter).

This (very) simple script will go though all applications `system_profiler` knows about and report if the application binaries have no match for the current system architecture:

```
#!/bin/bash
IFS=$'\n'
systemarch=$(uname -m)
for apppath in $(system_profiler SPApplicationsDataType | grep 'Location:' | awk -F ": " '{print $NF}'); do
    apparch=$(mdls -raw -name kMDItemExecutableArchitectures "${apppath}")
    echo ${apparch} | grep -q ${systemarch}
    if [[ $? -ne 0 ]]; then
        echo "${apppath} has no native binary for ${systemarch}"
        echo "${apparch}"
    fi
done
```

`uname -m` tells us the current system architecture  
`mdls -raw -name kMDItemExecutableArchitectures /some/file.app` tells us the architecture(s) that the app is compiled for which could be `ppc`, `i386`, `x86_64` or `arm64`

Sample output from the future might look something like this:

```
/Applications/dosbox.app has no native binary for arm64
(
    ppc,
    i386,
    "x86_64"
)
```

You could use this as a basis for your own scripts or to perhaps instead check for apps that _do_ match the host architecture or have multiple architectures (aka universal binaries).

Happy scripting.
