## Running python scripts as the user in JAMF

This is a quick one and one I use from time to time (file under "documenting little things I do but haven't written down" - may already be common knowledge even)

Every so often I want to run a bunch of stuff as the user. Jamf scripts have the handy feature where argument 3 to the script is the username. If it's a simple one line thing then usually "sudo -u $3 ..." in a bash script will do the trick but sometimes I want a whole script to be run as the user. Rather than shell out the entire thing I put this at the top of the script

```
#!/usr/bin/python
import pwd, os

uname = sys.argv[3]
uid = pwd.getpwnam(uname)[2]

os.setuid(uid)

...
```

And now the rest of the script runs in the context of the user. Obviously this makes no sense when running in other contexts but for self service items it's handy.
