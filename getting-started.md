---
layout: default
title: Getting Started
permalink: /go/
---

NOTE: This guide is still a work in progress.

# Getting Started with Hammerspoon

## What is Hammerspoon?

Hammerspoon is a desktop automation tool for OS X. It bridges various system level APIs into a Lua scripting engine, allowing you to have powerful effects on your system by writing Lua scripts.

## What is Lua?

Lua is a simple programming language. If you've never programmed in Lua before, you may want to run through [Learn Lua in Y minutes](http://learnxinyminutes.com/docs/lua/) before you begin.

## Let's go!

 * Download the latest release of Hammerspoon from [here](http://www.hammerspoon.org/) and drag it to your `/Applications` folder.
 * Run Hammerspoon.app and follow the prompts to enable Accessibility access for the app
 * Click on the Hammerspoon menu bar icon and choose `Open Config` from the menu
 * Open the Hammerspoon [API docs](http://www.hammerspoon.org/docs/) in your browser, to explore the extensions/functions available to you

## Contents

 * [Hello World](#helloworld)
 * [Introduction to window movement](#winmoveintro)
 * [A quick aside on colon syntax](#colonsyntax)
 * [More complex window movement](#winmovenethack)
 * [Window resizing](#winresize)

### <a name="helloworld"></a>Hello World

All good programming tutorials start with a Hello World example of some kind, so we'll use Hammerspoon's ability to bind keyboard hotkeys to demonstrate saying Hello World with a simple notification.

In your `init.lua` place the following:

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "W", function()
      hs.alert.show("Hello World!")
    end)

Then click on the Hammerspoon menubar icon and click `Reload Config`. You should now find that pressing `cmd+alt+ctrl+W` will display a Hello World notification on your screen.

What is happening here is that we're telling Hammerspoon to bind an anonymous function to a particular hotkey. The hotkey is specified by a table of modifier keys (cmd, alt and ctrl in this case) and a normal key (W). An anonymous function is simply one that doesn't have a name. We could have defined the alert function separately with a name and passed that name to `hs.hotkey.bind()`, but Lua makes it easy to define the functions inline.

### <a name="winmoveintro"></a>Introduction to window movement

One of the most useful things you can do with Hammerspoon is to manipulate the windows on your screen. We'll start off with a simple example and build up to something more complicated.

Add the following to your `init.lua`:

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()

      f.x = f.x - 10
      win:setframe(f)
    end)

This will now cause `cmd+alt+ctrl+H` to make move the currently focused window 10 pixels to the left. You can see that we fetch the currently focused window and then obtain its frame. This describes the location and size of the window. We can then modify the frame and apply it back to the window using `setframe()`.

### <a name="colonsyntax"></a>A quick aside on colon syntax

You might have noticed that sometimes we're using dots in function calls, and sometimes we're using colons. The colon syntax is a shorthand. The two following calls are identical:

    win:frame()
    hs.window.frame(win)

It's up to you if you want to use the colon syntax or not, but it can save a lot of typing!

### <a name="winmovenethack"></a>More complex window movement

We can build on the simple window movement example to implement a set of keyboard shortcuts that allow us to move a window in all directions, using the `nethack` movement keys:

    y   k   u
    h       l
    b   j   n

To do this, we simply need to repeat the previous `hs.hotkey.bind()` call with slightly different frame modifications:

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Y", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
      
      f.x = f.x - 10
      f.y = f.y - 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "K", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
      
      f.y = f.y - 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "U", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
      
      f.x = f.x + 10
      f.y = f.y - 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
      
      f.x = f.x - 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "L", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
    
      f.x = f.x + 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "B", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
    
      f.x = f.x - 10
      f.y = f.y + 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "J", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
    
      f.y = f.y + 10
      win:setframe(f)
    end)
    
    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "N", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
    
      f.x = f.x + 10
      f.y = f.y + 10
      win:setframe(f)
    end)

Try it out!

### <a name="winresize"></a>Window sizing

In this section we'll implement the common window management feature of moving a window so it occupies either the left or right half of the screen, allowing you to tile two windows next to each other for Productivity™.

    hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Left", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
      local screen = win:screen()
      local max = screen:frame()
      
      f.x = max.x
      f.y = max.y
      f.w = max.w / 2
      f.h = max.h
      win:setframe(f)
    end)

Here we are binding `cmd+alt+ctrl+Left` (as in the left cursor key) to a function that will fetch the focused window, then fetch the screen that the focused window is on, fetch the frame of the screen (note that `hs.screen.frame()` does not include the menubar and dock, see `hs.screen.fullframe()` if you need that) and set the frame of the window to occupy the left half of the screen.

To round that out, we'll add a function to move the window to the right half of the screen:

    hotkey.bind({"cmd", "alt", "ctrl"}, "Right", function()
      local win = hs.window.focusedwindow()
      local f = win:frame()
      local screen = win:screen()
      local max = screen:frame()
      
      f.x = max.x + (max.w / 2)
      f.y = max.y
      f.w = max.w / 2
      f.h = max.h
      win:setframe(f)
    end)

A good exercise here would be to see if you can now write functions for yourself that bind the Up/Down cursor keys to resizing windows to the top/bottom half of the screen, respectively.

# Credits

This guide owes a huge debt to Joseph Holsten and his [Mjolnir guide](http://blog.josephholsten.com/post/scripting-your-mac-getting-started)
