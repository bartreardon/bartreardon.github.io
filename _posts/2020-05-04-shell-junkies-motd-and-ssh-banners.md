## Shell Junkies - MOTD and SSH banners

macOS presents a wonderful graphical UI but there are times where we log on, copy files or perform other work via the command line over SSH. The default experience for generic logins is rather plain but we can jazz it up a little.

The first is by using the file `/etc/motd` (or Message Of The Day). This is simply a text file and whatever is in it will be displayed after login. It's a bit of a misnomer as without anything else the file is static, unchanging file rather than something inherently “daily” but with a bit of imagination one can embellish and update the file on a regular basis. for example:

```
#!/bin/sh
echo "You have logged on to $(hostname)" > /etc/motd
echo "Today's date is $(date +"%d %b %Y")" >> /etc/motd
echo "The weather for today is: $(curl -s 'wttr.in?format=3')" >> /etc/motd
```

(A fairly simple script but will do as a demonstration).

- Save this file to an appropriate location such as "`/usr/local/motd_updater.sh`".
- Set permissions with "`sudo chmod 744 /usr/local/motd_updater.sh`"
- Create a launchd to run it once a day or whatever schedule you prefer - [an example here](http://launched.zerowidth.com/plists/f4774c00-702e-0138-55ba-0d326bcef2a0) - and place it in `/Library/LaunchDaemons/` and load with `sudo launchctl load /Library/LaunchDaemons/com.motd_update.plist` (or whatever name you gave it)

Now, whenever someone connects via ssh they will be greeted with the a message that will update daily.

![](https://breardon.home.blog/wp-content/uploads/2020/05/screenshot-from-2020-05-04-22-08-38.png?w=786)

This is great, but a user won't see the message until AFTER login. What if you want to display a message BEFORE login, such as an Acceptable Use Policy? Fortunately that's easy enough to do as well as SSHD has a method for displaying a banner on connection. Much like MOTD, the banner is just a plain text file and you can tell SSHD to display it prior to prompting for a password.

For a basic banner, edit the file "`/etc/banner`" and copy in the following:

```
  -----------------------
  Conditions of Use


  WARNING: Your access to the system must be authorised.
            Unauthorised access may be prosecuted.


  By accessing this system, you agree that:
  - your actions may be monitored
  - you must abide by the MY ORG Code of Conduct and any Acceptable Use Policy
 
  ----------------------  
```

- edit the file "`/etc/ssh/sshd_config`"
- find the line "`#Banner /etc/banner`", uncomment and save
- "`sudo launchctl kickstart -k system/com.openssh.sshd`" to restart sshd

Now when someone logs in via SSH, they will see the text displayed in `/etc/banner` prior to entering their password. Very handy for presenting things like acceptable use policies:

![](https://breardon.home.blog/wp-content/uploads/2020/05/screenshot-from-2020-05-04-22-22-55-1.png?w=786)

As with `/etc/motd`, you can modify `/etc/banner` as you wish with a script or the like to keep it up to date as details change, even splash a bit of colour around with [ANSI escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code). Have Fun.
