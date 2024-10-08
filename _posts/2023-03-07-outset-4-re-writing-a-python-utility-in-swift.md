## Outset 4 - Re-writing a python utility in Swift

> This script scripts and installs pkgs at boot and/or login.
> 
> Joseph Chilcote 2014

So says the explanatory comment at the top of [version v1.0.0](https://github.com/chilcote/outset/releases/tag/v1.0.0) of outset, a python script written by [Joseph Chilcote](https://github.com/chilcote) and released in February 2015 to assist mac admins with running login scripts and package installs. Since then there have been two major releases with [v2](https://github.com/chilcote/outset/releases/tag/v2.0.0) solidifying the fundamentals of how the logic works and [v3](https://github.com/chilcote/outset/releases/tag/3.0.0) moving to python 3, anticipating what eventually came to pass in 2022 with the complete removal of python 2 from macOS. The last minor update was released in December 2020.

![](https://breardon.home.blog/wp-content/uploads/2023/03/outset.png_128x128402x.png?w=256)

Outset provides a solution to a problem that many in the mac admin community struggle with, and that is providing a simple scheduler for running boot and login scripts on device, without needing to know exactly [how launchd works](https://www.launchd.info/). Places scripts or packages into one of the processing directories and Outset takes care of running them and how often. It does however come with the dependency of python and with macOS no longer having a default python interpreter installed out of the box, it is up to the admin to install one of the [many](https://github.com/macadmins/python) [pythons](https://www.python.org/downloads/mac-osx/) [available](https://formulae.brew.sh/formula/python@3.9) resulting in a form of "[python roulette](https://github.com/chilcote/outset/blob/master/scripts/postinstall#L30-L40)" in order to get things going.

[Outset 4](https://github.com/macadmins/outset) is my attempt at answering two different problems. Firstly making the utility standalone with no required dependencies. The second is to continue to provide admins an easy way to run login and boot scripts and manage [how they appear](https://apple.stackexchange.com/questions/449441/where-do-the-allow-in-the-background-login-items-in-the-macos-ventura-system-s) as login items under macOS 13 and newer.

![](https://breardon.home.blog/wp-content/uploads/2023/03/image-7-3-2023-at-9.07-pm.jpg?w=526)

So In January 2023 I ported the existing outset v3.0.3 to Swift and through the early days of March I've been adjusting the code and how things operate and am happy to be at the stage where [beta 1](https://github.com/macadmins/outset/releases/tag/v4.0-Beta-1) is ready to see the light of day. Along with this is the move (with [Joseph's blessing](https://mastodon.social/@chilcote/109972501065747699)) to [Mac Admins Open Source](https://macadmins.io), which was [announced recently](https://www.macadmins.org/news/mac-admins-open-source) as a community repository supported by the [Mac Admins Foundation](https://www.macadmins.org/about-the-mac-admins-foundation) offering code signing and notarizarion for open source projects used, and developed, by mac admins, for mac admins.

With the move to swift there are many changes in operation so if you're used to outset you may want to read the notes accompanying [recent releases](https://github.com/macadmins/outset/releases) and [read the wiki](https://github.com/macadmins/outset/wiki/FAQ) to see what's changed.

I welcome any constructive feedback or feature requests, or if you are able to, [please consider contributing](https://github.com/macadmins/outset/wiki/Contributing) to the project.

You can reach me on @bartreardon or check out the #outset channel on the [macadmins slack](https://www.macadmins.org). I can also be found on mastodon at [https://aus.social/@bartreardon](https://aus.social/@bartreardon).

🙂
