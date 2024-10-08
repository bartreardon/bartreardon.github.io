## Install Microsoft O365 Apps direct from CDN via script in Jamf (or munki for that matter)

Hate trying to keep up to date with Office updates? Couldn't be bothered importing gigabytes of data into your local software repo? Annoyed that deploying Office via VPP automatically takes ages and is seemingly random? Download direct from Microsoft's CDN - and do it from a re-usable script as part of a jamf policy or munki no-pkg - and have it update [DEPNotify](https://gitlab.com/Mactroll/DEPNotify) status with a percentage count as it downloads and also as it's installing. 👍

https://github.com/bartreardon/jamfscripts/blob/master/download\_o365\_apps.sh

- ![](https://breardon.home.blog/wp-content/uploads/2019/10/screen-shot-2019-10-11-at-12.18.07-am.png?w=698)
- ![](https://breardon.home.blog/wp-content/uploads/2019/10/screen-shot-2019-10-11-at-12.18.25-am.png?w=699)

This is my script and I wrote it for my environment - it comes without warranty but feel free to use it, copy it, modify it, burn it or sacrifice it to the gods. There are bits of this I found on the internet and I can't remember where I got it from (the pulling % downloaded from curl output) - I'll update here for proper attribution when I find where I got that....on stackoverflow somewhere.

I use this in my DEP process as I found installing VPP from the app store was hit and miss. It could be re-purposed for self service or some other use. It presumes DEPNotify is already running (which in my case it is)

If you want to try it out, add this to a policy and pass "Word", "Outlook", "Excel" or "PowerPoint" as a parameter and it will (should) find the download link, kick off the install and clean up after itself.

Not tested as a nopkg in munki (yet) but there shouldn't be a reason it couldn't work with some minor modifications.
