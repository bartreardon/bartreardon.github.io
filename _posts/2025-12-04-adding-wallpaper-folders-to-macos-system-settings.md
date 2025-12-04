---
layout: post
title: "Adding Wallpaper folders to macOS System Settings"
date: 2025-12-04
---

As an admin I like adding some brand flair to our macOS deployments. I also like giving end users the option to choose their flavour of flair (or none at all). When setting default desktop backgrounds there are tools and methods to get images installed, set, and made available.

In macOS 26 Tahoe, one of those broke.

<!--more-->

### TL;DR

I wrote an app/swift package that lets you easily add a folder of images to System Settings -> Wallpaper under macOS 26

It's here [WallpaperFolderManager](https://github.com/bartreardon/WallpaperFolderManager)

If you're interested to know why, read on

### Simpler Times

For setting a desktop background there's nothing easier than [desktoppr](https://github.com/scriptingosx/desktoppr). Install the package, give it a nice image and boom, background set. It does more than that of course. You should head over there and check it out.

If you want to dump a bunch of images on the drive so people can select a random one there are a couple of options:

#### Plonk everything into `/Library/Desktop Pictures/`

This one is easy. Create `/Library/Desktop Pictures/` if it doesn't exist then fill it with images.

The problem with this is the images appear in System Settings -> Wallpaper -> Pictures after all the built in pictures. To see any of them you have to click "Show All" and then scroll past all the included pictures before you get to your custom ones.


#### Edit `~/Library/Preferences/com.apple.systempreferences.plist`

This was a little more convoluted but still easy. 

As a user you can add these yourself. In System Settings -> Wallpaper, clicke the "Your Photos" -> "Add Photo..." button, select "Choose Folder" and then select your folder of images. This places them in their own section at the bottom underneath the "Colours" section but they are at least visible with the folder name and preview of the images within.

From a script you could create this link by modifying the plist:

```bash
/usr/libexec/PlistBuddy -c "Add :DSKDesktopPrefPane:UserFolderPaths: string '/opt/Org Name/Org Pictures'" ~/Library/Preferences/com.apple.systempreferences.plist
```

This adds an item to the nested `DSKDesktopPrefPane -> UserFolderPaths` array.

```
% defaults read ~/Library/Preferences/com.apple.systempreferences.plist
{
    ...
    DSKDesktopPrefPane =     {
        UserFolderPaths =         (
            "/opt/Org Name/Org Pictures"
        );
        VisibleAlbums =         (
        );
    };
    ...
}
```

The `defaults` command is relatively unwieldy for this so we use `PlistBuddy` instead. This works fine from macOS 13 to macOS 15.

### macOS 26 - Because why do things the simple way?

In macOS 26 the configuration now lives in `~/Library/Containers/com.apple.wallpaper.extension.image/` with a bunch of embedded plists as data blobs and some other configuration and there is a daemon called `WallpaperAgent` looking after it. Unsure if this is new in macOS 26 but I'm aware of it now.

Managing the new config from a shell command using defaults or plistbuddy would be rather annoying at best as there is a lot to consider and get right. I know this becasue I initially tried to work out where the settings were changed so I could get our custom folder of desktop backgrounds working. Shout out to [FSMonitor](https://fsmonitor.com) which gave some rather enlightening discoveries. 

    When life gives you lemons, write a swift app

Enter [WallpaperFolderManager](https://github.com/bartreardon/WallpaperFolderManager) (names are hard ok)

WallpaperFolderManager is a swift utility/package that handles all the work of creating the necessary files, plists, encoding etc required to be able to programatically add in a folder path to System Settings -> Wallpaper. It will create the required config and then restart `cfprefsd` and `WallpaperAgent`

_Note: At the time of writing I'm not aware of any public API's that hook in to container management. If there is one, let me know_

### Usage

```bash
# Add a folder
wallpaper-folder add ~/Pictures/Wallpapers

# Add a folder with verbose output
wallpaper-folder add ~/Pictures/Wallpapers --verbose

# Add a folder without restarting services
wallpaper-folder add ~/Pictures/Wallpapers --no-restart

# List registered folders
wallpaper-folder list

# List with verbose output (shows IDs and dates)
wallpaper-folder list --verbose

# Remove a folder
wallpaper-folder remove ~/Pictures/Wallpapers

# Show help
wallpaper-folder --help
wallpaper-folder add --help
```


## Results

Before:

<img width="350" alt="before" src="https://github.com/user-attachments/assets/58ae6d6e-bd2d-4738-a56d-b38c0686221c" />

<br>

After: `% wallpaper-folder add ~/Pictures/My\ Pics`

_Note: Requires closing and re-openeing System Settings if it was open to read the new settings_

<img width="350" alt="after" src="https://github.com/user-attachments/assets/2dbb9670-35b7-4650-8330-45afb775f136" />

## Caveats

I've tested this on macOS 15 and macOS 26. It _should_ work on macOS 13 and 14 but I haven't tried. On the older OS's it falls back to modifying the `com.apple.systempreferences` which is (AFAIK) the same on macOS 13->15

It doesn't support macOS 12 or earlier (and neither should you)

## How to Get

Go to the [releases page](https://github.com/bartreardon/WallpaperFolderManager/releases) 

There is a signed and notarized pkg (installs to `/usr/local/bin`) or standalone binary

It is also availabe as a swift package if you want to include this functionality in your own app. Simply add to your `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/bartreardon/WallpaperFolderManager.git", from: "1.0.0")
]
```

