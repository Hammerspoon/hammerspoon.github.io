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

All good programming tutorials start with a Hello World example of some kind, so we'll use Hammerspoon's ability to bind keyboard hotkeys to demonstrate saying Hello World with a simple notification.

In your `init.lua` place the following:

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "W", function()
      hs.alert.show("Hello World!")
    end)

Then click on the Hammerspoon menubar icon and click `Reload Config`. You should now find that pressing `cmd+alt+ctrl+W` will display a Hello World notification on your screen.

What is happening here is that we're telling Hammerspoon to bind an anonymous function to a particular hotkey. The hotkey is specified by a table of modifier keys (cmd, alt and ctrl in this case) and a normal key (W). An anonymous function is simply one that doesn't have a name. We could have defined the alert function separately with a name and passed that name to `hs.hotkey.bind()`, but Lua makes it easy to define the functions inline.

### Window movement

One of the most useful things you can do with Hammerspoon is to manipulate the windows on your screen. We'll start off with a simple example and build up to something more complicated.

Add the following to your `init.lua`:

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()

      f.x = f.x - 10
      win:setframe(f)
    end)

This will now cause `cmd+alt+ctrl+H` to make move the currently focused window 10 pixels to the left. You can see that we fetch the currently focused window and then obtain its frame. This describes the location and size of the window. We can then modify the frame and apply it back to the window using `setframe()`.

#### A quick aside on colon syntax

You might have noticed that sometimes we're using dots in function calls, and sometimes we're using colons. The colon syntax is a shorthand. The two following calls are identical:

    win:frame()
    hs.window.frame(win)

It's up to you if you want to use the colon syntax or not, but it can save a lot of typing!
