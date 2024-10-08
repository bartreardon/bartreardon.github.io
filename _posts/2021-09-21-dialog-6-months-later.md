## Dialog 6 months later

**How It Started**

https://twitter.com/bartreardon/status/1369237760842887170?s=20

A bit over six months ago I had one of those bursts of inspiration that only comes at the most inopportune of times. The basis of the idea was to make an app that would display a message to my mac users that looked nice and was customisable with text and images. SwiftUI was on the list of things I wanted to learn and so Dialog was born.

Two days later on the 11th March 2021 the first beta version was released on GitHub as a binary and the following day [I wrote a blog](https://breardon.home.blog/2021/03/12/dialog-an-app-for-showing-a-dialog/) post announcing it. The source was released on the 20th after clearance from my employer.

The initial version was fairly basic at the time but did what I wanted it to do. Displayed a window where I could control the title, message, button labels and include an image (or icon). It was one fixed size as I wasn't up to speed with SwiftUI's declarative layout style and making it do what I wanted, but it was a start and we all have to start somewhere.

https://twitter.com/bartreardon/status/1370149913271488515?s=20

![](https://breardon.home.blog/wp-content/uploads/2021/09/screen-shot-2021-09-21-at-10.44.56-pm.png?w=862)

**How it's going**

Six months later and I have been nerd sniped with features and created an app that I think does about as much as anyone (a mac admin at least) could want in an app who's task is to display a message to the current user.

Apart from what it started with, Dialog now displays selectable dropdown lists, has text entry, fully customisable text colours and font sizes, support for SF Symbols as an icon, can display another icon as an overlay to the main icon, built in markdown support, can display arbitrary window sizes, has background images, full screen display mode, can timeout after a specified number of seconds (with a custom timeout indicator because why not), text justification, banner images, can provide json output and last but not least, supports a good chunk of the command line arguments from jamfHelper making it easy to drop in replace and not require an admin to re-write existing scripts (too much...it does it's best and there are some options not currently supported). All in an app thats fully self contained and less than 2MB in size.

![](https://breardon.home.blog/wp-content/uploads/2021/09/screen-shot-2021-09-21-at-11.21.15-pm.png?w=1024)

Along the way I've learned more about SwiftUI than I thought I ever would and have used what I have learned in other projects, contributing to [Nudge v1.1.1](//github.com/macadmins/nudge) with a refactor in how its layout is presented by SwiftUI and making future support for arbitrary window sizes easier to implement in that utility.

As is evidenced from the progress in Dialog over the last 6 months, I'm more than open to feature suggestions and bug reports. I have no immediate plans to slow down development and with this first SwiftUI app ticking along nicely I have ideas for a few more apps and utilities.

Dialog is also in the [Jamf Marketplace](https://marketplace.jamf.com/details/dialog) where it has been getting a semi respectable level of attention in terms of generating traffic (If you are using dialog, please leave a review) and the [latest release](https://github.com/bartreardon/Dialog-public/releases) can be found on the Dialog GitHub page [https://github.com/bartreardon/Dialog-public](https://github.com/bartreardon/Dialog-public)

If you check it out, please send through feedback or join the #dialog channel on the macadmins slack.

🙂
