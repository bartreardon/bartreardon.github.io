---
layout: post
title: "Script compacting script"
date: 2025-04-10
---

![Image](https://github.com/user-attachments/assets/7b593a1c-dc5f-4447-a75a-1f89c97ab6ae)

I was thinking recently about very large shell scripts and ways to make them more compact for use in Jamf or other MDM's for macOS.

So I nerd sniped myself made a script that compacts other scripts.

<!--more-->

### Why

This came to mind when working with [Installomator](https://github.com/installomator/Installomator/) which in version 10.8 is 517Kb in size and over 11k lines long.

This isn't a problem in reality but things like the inline editor in the Jamf Pro UI tend to choke on stuff like this. Large scripts aren't _that_ uncommon in this context but half a meg is pretty large. Not that compressing and encoding the script helps any since the resulting data is pretty uneditable. 

### How 

The solution I came up with is pretty simple:

 - Use bzip2 compression at level 9 to compress the entire script
 - Use base64 encoding to convert the compressed data to ascii
 - Store the data as a blob in another script that performs the decompression and execution

Initially I did this by moving things into and out of temp files but that has the potential to leave junk behind if something doesn't work as you would expect and cleanup doesn't happen but fortunatly we can do everything with pipes.

Creating the data representation of the target script is pretty easy and we can use `bzcat` to do the compression and pass the result to `base64` to encode:

```
data=$(/usr/bin/bzcat -zk9 "$inputFile" | /usr/bin/base64 -w120)
```

Once we have that, we just do the reverse and execute. This can be done as a one liner:

```
# base64 decode the data blob
# pipe through to bzcat to decompress
# pipe through to a zsh subshell with any arguments

/usr/bin/base64 -d -o - <<< "$data" | /usr/bin/bzcat | /bin/zsh -s "$@"
```

This worked pretty well and in the case of the Installomator script reduced from 517Kb, 11,179 line file to 122Kb, 1038 lines.

### Where

Here's my script that takes another script as an argument and creates the compacted version, ready to run.

[compress.sh](https://github.com/bartreardon/macscripts/blob/master/compress.sh)

### Conclusion

Is this useful? Who knows. Maybe some scripts will hit a limit of what can be stored or delivered using MDM processes or maybe you just want to obfuscate the contents of a script or protect it from modification.

This was created more as a thought exercise but maybe someone has a use for it. There's no harm in putting this out there and just the process of thinking about a problem and finding a solution is a neat thing to do.

### Related

Speaking of scripts that may or may not have any utility but were fun to think about and create, this is my script for creating [self extracting scripts](https://github.com/bartreardon/macscripts/blob/master/create_self_extracting_script.sh) that extract themselves to a given target location. 

Maybe useful if you need to install a script somewhere but can't (or don't want to) create a .pkg to do the deployment for you.
