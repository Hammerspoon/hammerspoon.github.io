---
layout: default
title: Contributors
---
#Hammerspoon Crash Data Privacy Policy

## Note
* Hammerspoon used to use Crashlytics for crash reports, but has since switched to Sentry. If you wish to consult our Privacy Policy from when we were using Crashlytics, see [here](https://github.com/Hammerspoon/hammerspoon.github.io/blob/761c843cd65c908ac2305961d8cb63ab385c8fab/privacy.md).

## Facts
* Hammerspoon uses Sentry to collect information about crashes in the application and extensions (hereafter known as `Crash Data`).
* This feature is enabled by default. Users can opt-out using the Hammerspoon preferences window.
* Crash Data is uploaded securely to the Sentry servers.
* No personally identifying data is collected or transmitted by Hammerspoon, with the possible exception of path names. If you run Hammerspoon from a path that contains your name (e.g. `/Users/MyRealNameIsBobAnderson/Applications/Hammerspoon.app` then your name would be included in Crash Data.
* No Sentry features have been enabled that collect or transmit any personal information about the user.
* Some information about the hardware and operating system is collected and transmitted.
* The location of the Hammerspoon app bundle in your filesystem, is collected (e.g. `/Applications/Hammerspoon.app`).
* The location of the Hammerspoon extensions you use, is collected (e.g. `/Applications/Hammerspoon.app/Contents/Resources/extensions/ipc/internal-ipc.so`).
* Your Hammerspoon configuration (i.e. `init.lua`) is not collected or transmitted, nor is any personal data that may be stored by Hammerspoon (e.g. the contents of your clipboard, or the contents of Lua variables).
* You can read the Sentry Privacy Policy at [their website](https://sentry.io/privacy/#third-party-links).

## Why do we do this?
 * It is very important to us that Hammerspoon contain as few bugs as possible, particularly ones which cause it to crash.
 * We need your help to make this happen, so we collect low level information about which part of Hammerspoon has caused a crash, so we can fix it.

## Promises
* We will never attempt to identify Hammerspoon users using the Crash Data.
* We will never sell any Hammerspoon Crash Data.
* We will never distribute any Hammerspoon Crash Data for any other purpose than the fixing of bugs (but for example we may share Crash Data with other developers who are assisting with fixing crash bugs).
* Some Crash Data may be distributed in the form of GitHub Issues (i.e. bug reports) if it is important/relevant for the discussion of a bug.

## If you do not want to upload Crash Data
* Click on the Hammerspoon menu icon and choose `Preferences`, untick `Send crash data`, quit Hammerspoon and re-open it. The Sentry framework will no longer be initialised. We understand that many people wish to protect their privacy, or may run Hammerspoon in an environment where transmitting any data is unacceptable. We ask that if at all possible, you help us to keep Hammerspoon as bug-free as possible, by enabling `Send crash data` if you can.

## Feedback
* If you have any suggestions or comments about this feature of Hammerspoon, or the contents of this Privacy Policy, please [file an issue on GitHub](https://github.com/Hammerspoon/hammerspoon/issues/new).
