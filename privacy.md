---
layout: default
title: Contributors
---
#Hammerspoon Crash Data Privacy Policy

## Facts
* Hammerspoon uses Crashlytics to collect information about crashes in the application and extensions (hereafter known as `Crash Data`).
* This feature is enabled by default. Users can opt-out using the Hammerspoon preferences window.
* Crash Data is uploaded securely to the Crashlytics servers.
* Crashlytics is owned by Twitter.
* No personally identifying data is collected or transmitted by Hammerspoon.
* No Crashlytics features have been enabled that collect or transmit any personal information about the user.
* Some information about the hardware and operating system is collected and transmitted.
* The location of the Hammerspoon app bundle in your filesystem, is collected (e.g. `/Applications/Hammerspoon.app`).
* The location of the Hammerspoon extensions you use, is collected (e.g. `/Applications/Hammerspoon.app/Contents/Resources/extensions/ipc/internal-ipc.so`).
* Your Hammerspoon configuration (i.e. `init.lua`) is not collected or transmitted, nor is any personal data that may be stored by Hammerspoon (e.g. the contents of your clipboard, or the contents of Lua variables).
* You can read the Crashlytics Privacy Policy at [their website](https://github.com/Hammerspoon/hammerspoon/issues/139#issuecomment-77907712).

## Why do we do this?
 * It is very important to us that Hammerspoon contain as few bugs as possible, particularly ones which cause it to crash.
 * We need your help to make this happen, so we collect low level information about which part of Hammerspoon has caused a crash, so we can fix it.

## Promises
* We will never attempt to identify Hammerspoon users using the Crash Data.
* We will never sell any Hammerspoon Crash Data.
* We will never distribute any Hammerspoon Crash Data for any other purpose than the fixing of bugs.
* Some Crash Data may be distributed in the form of GitHub Issues (i.e. bug reports) if it is important/relevant for the discussion of a bug.

## If you do not want to upload Crash Data
* Click on the Hammerspoon menu icon and choose `Preferences`, untick `Send crash data`, quit Hammerspoon and re-open it. The Crashlytics framework will no longer be initialised.

## Feedback
* If you have any suggestions or comments about this feature of Hammerspoon, or the contents of this Privacy Policy, please [file an issue on GitHub](https://github.com/Hammerspoon/hammerspoon/issues/new).
