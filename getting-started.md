---
layout: default
title: Getting Started
permalink: /go/
---

# Getting Started with Hammerspoon

## What is Hammerspoon?

Hammerspoon is a desktop automation tool for OS X. It bridges various system level APIs into a Lua scripting engine, allowing you to have powerful effects on your system by writing Lua scripts.

## What is Lua?

Lua is a simple programming language. If you've never programmed in Lua before, you may want to run through [Learn Lua in Y minutes](http://learnxinyminutes.com/docs/lua/) before you begin.

## Let's go!

 * Download the latest release of Hammerspoon from [here](http://www.hammerspoon.org/) and drag it to your `/Applications` folder.
 * Run Hammerspoon.app and follow the prompts to enable Accessibility access for the app
 * Make a directory in your home directory called `.hammerspoon` (note the dot at the front of the name).
 * Fire up your favourite text editor and create a file called `.hammerspoon/init.lua`

### Hello World

In your `init.lua` place the following:

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
      hs.alert.show("Hello World!")
    end)

Then click on the Hammerspoon menubar icon and click `Reload Config`. You should now find that pressing `cmd+alt+ctrl+H` will display a Hello World notification on your screen.
