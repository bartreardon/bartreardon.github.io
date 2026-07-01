---
tags: macOS
---

## Electron and Squirrel.framework suck...

...your laptop battery and make you fans spin hard if you don't jump when the updater shows up.

![macOS helper tool authorisation prompt triggered by a Squirrel-based Electron app updater](/images/electron-and-squirrel-framework-suck-screen-shot-2021-01-20-at-1.40.11-pm.png)

Recognise that dialog? - Many apps pop up the same type, Lock icon with the App icon overlaid and the text "An update is ready to install. <AppName> is trying to add a new helper tool"

Well if you're lazy and don't get to it, or it pops up when you're not at your computer, you may find that the fans in your Mac are making noises. A quick check of Activity Monitor confirm that the app in question is taking up a good chunk of your CPU, but doing what exactly?

Spinning wheels it turns out calling the same method repeatedly as long as the dialog is waiting to be dismissed. https://soundmacguy.wordpress.com/2018/02/25/microsoft-skype-definitely-and-teams-maybe-disabling-automatic-updates/ (thank you to Nathaniel Strauss over on the macadmins slack for pointing this out)

Squirrel.framework is a common update framework unsed in Electron based apps like Atom, Discord, Microsoft Teams, Visual Studio Code and Signal among others. And it has a bug

![GitHub issue titled Squirrel-based Install Dialogue Burns CPU/Fans on Mac](/images/electron-and-squirrel-framework-suck-screen-shot-2021-01-20-at-8.46.12-pm.png)

https://github.com/Squirrel/Squirrel.Mac/issues/247

Not so encouraging though...

![GitHub issue comment noting the maintainer hasn't worked on the Squirrel codebase in years](/images/electron-and-squirrel-framework-suck-screen-shot-2021-01-20-at-8.46.52-pm.png)

So, if you were like me and were wondering why - now you know. If you know anyone that works on Electron apps for Mac and has patched this issue, can you send it upstream please? 👍
