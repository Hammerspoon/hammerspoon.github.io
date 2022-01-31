---
layout: default
title: Hammerspoon
---

# Hammerspoon

## What is Hammerspoon?

This is a tool for powerful automation of macOS. At its core, Hammerspoon is just a bridge between the operating system and a Lua scripting engine.
What gives Hammerspoon its power is a set of extensions that expose specific pieces of system functionality, to the user.

## What can it do for me?

You can write Lua code that interacts with macOS APIs for applications, windows, mouse pointers, filesystem objects, audio devices, batteries, screens, low-level keyboard/mouse events, clipboards, location services, wifi, and more.

If you want to explore the options Hammerspoon offers, check out the [Getting Started Guide](/go/) and the [full API documentation](/docs/) as well as the already pre-made plugins called [Spoons](https://www.hammerspoon.org/Spoons/).

Typically you would write a configuration file in Lua that connects events to actions. You might want to bind a keyboard shortcut to a series of window operations, or an applescript. You might want to run a series of commands when your wifi interface connects to your home network. You might want to display an alert when your battery drops below a certain percentage. You might want to do something crazy like have iTunes automatically start playing when your Mac detects you are in Paris.

## How do I install it?

[Download the latest release](https://github.com/Hammerspoon/hammerspoon/releases/latest) and then drag the application to `/Applications/`.

## How do I use it?

Out of the box, Hammerspoon does nothing. You will need to create a Lua script in  `~/.hammerspoon/init.lua` using our APIs and standard Lua APIs.

If you are new to Hammerspoon, read the [Getting Started Guide](/go/) with reference to the [full API documentation](/docs/).

You can learn more about the Lua scripting language at [lua.org](https://www.lua.org/docs.html).

## How can I contribute?

More extensions will always be a huge benefit to Hammerspoon. They can either be pure Lua scripts that offer useful helper functions, or you can write Objective-C extensions to expose new areas of system functionality to users. For more information, see [the contribution guidelines on GitHub](https://github.com/Hammerspoon/hammerspoon/blob/master/CONTRIBUTING.md).

Bugs found on http://www.hammerspoon.org/ can be reported on [GitHub](https://github.com/Hammerspoon/hammerspoon.github.io)

## Where can I get help?

You can usually get a quick answer in our IRC channel, #hammerspoon on [Libera](https://libera.chat).

We have a Google Group mailing list [here](https://groups.google.com/forum/#!forum/hammerspoon/)

If you find a bug, or have a suggestion, you can also file an issue on the [issue tracker](https://github.com/Hammerspoon/hammerspoon/issues).

## Who made this?

Lots of people! You can find out more on our [Contributors](/contributors.html) page.
