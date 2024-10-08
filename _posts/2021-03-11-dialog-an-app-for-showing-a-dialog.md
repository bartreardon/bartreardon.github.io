## "“Dialog” - An app for showing a dialog

Why a dialog app? Two reasons, the tool I have been using to this point was coming up short on some features I wanted to have, and I wanted an excuse to get my teeth stuck into a SwiftUI project. Also, I wanted to finally get around to releasing a tool for the mac admin community that is hopefully useful (I have a number that are useful, just not released)

3 days later...Meet Dialog.

The app has a simple premise, show a dialog with a title, some text, one or two buttons and optionally an image. I took inspiration in the UNIX philosophy of having one tool do one job. Everything Ic can do can be specified on the command line and the app will return different exit codes depending on what action the user takes.

At its most basic, it looks like this:

<figure>

![](https://breardon.home.blog/wp-content/uploads/2021/03/image.png?w=720)

<figcaption>

  


</figcaption>

</figure>

Which is pretty boring, fortunately you can customise all the options. The three actions a user can take are:  
1 - Press the \[OK\] button (or hit <Enter> on their keyboard)  
2 - Press the \[Cancel\] button (or hit <ESC>)  
3 - Press the \[More Information\] button.  
Each action is closes the app with exit code of 0, 2 or 3. The \[OK\] and \[More Information\] buttons can optionally also redirect to a URL.

The Title and Message areas can be specified of course. The icon image can be either a jpg or png from a file or a URL. If the image is square (e.g. a jpg) the corners are slightly rounded for a more pleasing look.

Put that together you can make something interesting:  

![](https://breardon.home.blog/wp-content/uploads/2021/03/image-1.png?w=720)

Dialog is only supported on macOS 11 (universal) at this point in time.

To check out a pre-release version, please visit:  
https://github.com/bartreardon/Dialog-public/blob/main/README.md  
  
Source code will be made available soon. <- Edit: Source is now available as of 20-03-2021
