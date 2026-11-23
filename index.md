---
layout: default
title: Hammerspoon
---

<div class="v2-banner">
  <h2>Hammerspoon 2 is here</h2>
  <p>Hammerspoon 2 is now the main version of Hammerspoon: a modern rewrite that you script in JavaScript instead of Lua. It's in beta, so expect a few rough edges, and please <a href="https://github.com/Hammerspoon/Hammerspoon2/issues">tell us about them</a>.</p>
  <p>
    <a class="btn btn-primary" href="https://github.com/Hammerspoon/Hammerspoon2/releases">Download the beta</a>
    <a class="btn btn-default" href="/v1/">Looking for Hammerspoon 1?</a>
  </p>
  <p class="v2-banner-note">Hammerspoon 1 isn't going anywhere: its <a href="/v1/">homepage</a>, <a href="https://github.com/Hammerspoon/hammerspoon/releases/latest">downloads</a>, <a href="/go/">Getting Started Guide</a> and <a href="/docs/">API documentation</a> are all still here.</p>
</div>

# Hammerspoon

## What is Hammerspoon?

This is a tool for powerful automation of macOS. At its core, Hammerspoon is just a bridge between the operating system and a JavaScript scripting engine (the same JavaScriptCore engine that Safari uses).
What gives Hammerspoon its power is a set of modules that expose specific pieces of system functionality, to the user.

## What can it do for me?

You can write JavaScript code that interacts with macOS APIs for applications, windows, hotkeys, the keyboard and mouse, screens and Spaces, the filesystem, audio devices, Bluetooth, Wi-Fi, notifications, AppleScript, Shortcuts, and more. You can even build your own windows and menus with `hs.ui`, or draw straight onto the screen with `hs.canvas`.

If you want to explore the options Hammerspoon offers, check out the [Getting Started Guide](/2/main/js/getting-started.html) and the [full API documentation](/2/), as well as the [Spoons guide](/2/main/js/spoons-guide.html) for ready-made plugins. If you prefer TypeScript, there's a [guide for that too](/2/main/js/typescript-guide.html).

Typically you would write a configuration file in JavaScript that connects events to actions. You might want to bind a keyboard shortcut to a series of window operations, or an AppleScript. You might want to run a series of commands when your Mac joins your home Wi-Fi network. You might want to display an alert when your battery drops below a certain percentage. You might want to do something crazy like have Music automatically start playing when your Mac detects you are in Paris.

## How do I install it?

Hammerspoon 2 requires macOS 27 or later. [Download the latest release](https://github.com/Hammerspoon/Hammerspoon2/releases) and then drag `Hammerspoon 2.app` to `/Applications/`. Once it's installed, it checks for updates itself.

Hammerspoon 2 is a separate app from Hammerspoon 1, with its own configuration, so you can keep Hammerspoon 1 installed while you try it out.

## How do I use it?

The first time you run Hammerspoon 2, it helps you choose where your configuration lives (typically `~/.config/Hammerspoon2/`) and creates an `init.js` there for you to edit.

If you are new to Hammerspoon, read the [Getting Started Guide](/2/main/js/getting-started.html) with reference to the [full API documentation](/2/). If you're coming from Hammerspoon 1, the [Migration Guide](/2/main/js/migration-guide.html) explains what's changed. The documentation is also built into the app: run `hs.docs.show()` in the Hammerspoon Console.

## How can I contribute?

Hammerspoon 2 is developed on [GitHub](https://github.com/Hammerspoon/Hammerspoon2). For more information, see [the contribution guidelines](https://github.com/Hammerspoon/Hammerspoon2/blob/main/CONTRIBUTING.md).

Bugs found on http://www.hammerspoon.org/ can be reported on [GitHub](https://github.com/Hammerspoon/hammerspoon.github.io)

## Where can I get help?

We have a Discord server to chat about Hammerspoon. [Click here to join](https://discord.gg/vxchqkRbkR)

If you find a bug, or have a suggestion, you can also file an issue on the [issue tracker](https://github.com/Hammerspoon/Hammerspoon2/issues).

## Who made this?

Lots of people! You can find out more on our [Contributors](/contributors.html) page, and see who has worked on Hammerspoon 2 [on GitHub](https://github.com/Hammerspoon/Hammerspoon2/graphs/contributors).
