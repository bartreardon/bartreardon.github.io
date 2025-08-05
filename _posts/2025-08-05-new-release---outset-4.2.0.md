---
layout: post
title: "New Release - Outset 4.2.0"
date: 2025-08-05
---

![](https://github.com/macadmins/outset/raw/78002def008bea2a51f793e0cd033fe71ff81c9b/Outset/Assets.xcassets/AppIcon.appiconset/Outset.png_128x128.png?raw=true)

Today I pushed a new release of Outset with a couple of neat new features

Get it at [https://github.com/macadmins/outset/releases](https://github.com/macadmins/outset/releases) 

<!--more-->

### What is Outset?

If you're new, Outset is a utility application which automatically processes scripts and packages during the boot sequence, user logins, or on demand. It is as alternative to creating and deploying custom launchd plists for a subset of common use cases, namely device start and user login events. Outset 4 is a swift rewrite of the origional which was written in python. It is maintained and released under [Mac Admins Open Source](https://macadmins.io) 

A while ago it was suggested we could add support for outset to run scripts deployed from a config profile. This required a bit of thought to implement in a nice way but I think what we have should work for most use cases.

Some considerations that were made:

 - Profile delivered scripts should disable local scripts
 - Scripts should not be directly exposed in the UI
 - Support for multiple profiles to enable conditional profile (and therefore conditional scripts) for MDM's that have that capability


### Script security

The first point has a similar function to deploying a profile with a [shasum](https://github.com/macadmins/outset/wiki/Security#file-verification-optional) of local scripts in that the presence of the config item disables scripts not identified from running. As a security feature this is useful to effectively disable local script execution. In addition, this is only supported for MDM delivered profiles. Payloads added to local preferences are ignored (except when using `--debug` which is intended for testing)

So that scripts are not direcetly visible in the macOS UI, the script is base64 encoded and deployed as data. In System Settings -> General -> Device Management this will appear something like this:

```
"login-every" =
{
"Test Script" = {length = 96, bytes
= 0x49794576 596d6c75 4c33707a 61416/4b ... 62475569 43676f3d};
};
```

Reading the contents using `defaults` returns

```
% defaults read /Library/Managed\ Preferences/io.macadmins.Outset.plist
{
    "script_payloads" =     {
        "login-every" =         {
            "Test Script" = {length = 96, bytes = 0x49794576 596d6c75 4c33707a 61416f4b ... 62475569 43676f3d };
        };
    };
}
```

This should protect script content from casual observers however absolute security cannot be guaranteed and determined individuals will still be able to discover the complete base64 encoded string and decode it.

### Multiple profile support

Supporting multiple profiles was tricky. macOS will happily load multiple profiles for the same preference domain but if two profiles contain the same parent key then at best, only one will load but I believe the behaviour is undefined. To get around this, profiles can be created with custom parent keys, prefixed with `script_payloads`.

This means you can have a single profile with `script_payloads` or multiple payloads each with a custom keyname, e.g. `script_payloads_testers`, `script_payloads_common`, `script_payloads_VIP` etc and deploy one or ore of these to devices as needed.

More details are available in the [Payloads page](https://github.com/macadmins/outset/wiki/Payloads) on the Outset wiki.

One drawback from this method though is that tools like [ProfileCreator](https://github.com/ProfileCreator/ProfileCreator) and [iMazing Profile Editor](https://imazing.com/profile-editor) or Jamf Pro custom schemas will not be able to support arbitary payload keys (apart from that, Jamf Pro Custom Schemas don't support `data` as an object type which makes it impossible to use for script payloads). I'll be looking at the [Profile Manifests project](https://github.com/ProfileManifests/ProfileManifests) to see if there is a compromise that can be made here.

## What next?

Outset will continue to be maintained to make sure it is current and works on modern versions of macOS in an expected way.

If you have any feedback, feature requests or have notices a bug or issue, please [create an issue](https://github.com/macadmins/outset/issues/new/choose) on the Outset github project page

You can also come chat in the `#outset` channel in the [MacAdmins Slack](https://www.macadmins.org) 🙂
