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

 * [Download the latest release of Hammerspoon](http://www.hammerspoon.org/) and drag it to your `/Applications` folder
 * Run Hammerspoon.app and follow the prompts to enable Accessibility access for the app
 * Click on the Hammerspoon menu bar icon and choose `Open Config` from the menu
 * Open the Hammerspoon [API docs](http://www.hammerspoon.org/docs/) in your browser, to explore the extensions we provide, and the functions they offer

## Contents

 * [Hello World](#helloworld)
 * [Introduction to window movement](#winmoveintro)
 * [A quick aside on colon syntax](#colonsyntax)
 * [More complex window movement](#winmovenethack)
 * [Window resizing](#winresize)
 * [Simple config reloading](#simplereload)
 * [Fancy config reloading](#fancyreload)
 * [Interacting with application menus](#appmenus)

### <a name="helloworld"></a>Hello World

All good programming tutorials start with a Hello World example of some kind, so we'll use Hammerspoon's ability to bind keyboard hotkeys to demonstrate saying Hello World with a simple notification.

In your `init.lua` place the following:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "W", function()
  hs.alert.show("Hello World!")
end)
```

Then click on the Hammerspoon menubar icon and click `Reload Config`. You should now find that pressing `cmd+alt+ctrl+W` will display a Hello World notification on your screen.

What is happening here is that we're telling Hammerspoon to bind an anonymous function to a particular hotkey. The hotkey is specified by a table of modifier keys (cmd, alt and ctrl in this case) and a normal key (W). An anonymous function is simply one that doesn't have a name. We could have defined the alert function separately with a name and passed that name to `hs.hotkey.bind()`, but Lua makes it easy to define the functions inline.

### <a name="winmoveintro"></a>Introduction to window movement

One of the most useful things you can do with Hammerspoon is to manipulate the windows on your screen. We'll start off with a simple example and build up to something more complicated.

Add the following to your `init.lua`:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
  local win = hs.window.focusedwindow()
  local f = win:frame()

  f.x = f.x - 10
  win:setframe(f)
end)
```

This will now cause `cmd+alt+ctrl+H` to make move the currently focused window 10 pixels to the left. You can see that we fetch the currently focused window and then obtain its frame. This describes the location and size of the window. We can then modify the frame and apply it back to the window using `setframe()`.

### <a name="colonsyntax"></a>A quick aside on colon syntax

You might have noticed that sometimes we're using dots in function calls, and sometimes we're using colons. The colon syntax is a shorthand. The two following calls are identical:

```lua
win:frame()
hs.window.frame(win)
```

It's up to you if you want to use the colon syntax or not, but it can save a lot of typing!

### <a name="winmovenethack"></a>More complex window movement

We can build on the simple window movement example to implement a set of keyboard shortcuts that allow us to move a window in all directions, using the `nethack` movement keys:

    y   k   u
    h       l
    b   j   n

To do this, we simply need to repeat the previous `hs.hotkey.bind()` call with slightly different frame modifications:

```lua
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
```

Try it out!

### <a name="winresize"></a>Window sizing

In this section we'll implement the common window management feature of moving a window so it occupies either the left or right half of the screen, allowing you to tile two windows next to each other for Productivity™.

```lua
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
```

Here we are binding `cmd+alt+ctrl+Left` (as in the left cursor key) to a function that will fetch the focused window, then fetch the screen that the focused window is on, fetch the frame of the screen (note that `hs.screen.frame()` does not include the menubar and dock, see `hs.screen.fullframe()` if you need that) and set the frame of the window to occupy the left half of the screen.

To round that out, we'll add a function to move the window to the right half of the screen:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Right", function()
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
```

A good exercise here would be to see if you can now write functions for yourself that bind the Up/Down cursor keys to resizing windows to the top/bottom half of the screen, respectively.

### <a name="simplereload"></a>Simple configuration reloading

You may have noticed that while you're editing the config, it's a little bit annoying to have to keep choosing the `Reload Config` menu item every time you make a change. We can fix that by adding a keyboard shortcut to reload the config:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "R", function()
  hs.alert.show("reloading config")
  hs.reload()
end)
```

We've now bound `cmd+alt+ctrl+R` to a function that will reload the config and display a simple alert banner on the screen for a couple of seconds.

One important detail to call out here is that `hs.reload()` destroys the current Lua interpreter and creates a new one, so any code you place after it in this function, won't ever get executed.

### <a name="fancyreload"></a>Fancy configuration reloading

So we can now manually force a reload, but why should we even have to do that when the computer could do it for us‽

The following snippet introduces another new extension, `pathwatcher` which will allow us to automatically reload the config whenever the file changes:

```lua
function reload_config(files)
    hs.alert.show("reloading config")
    hs.reload()
end
hs.pathwatcher.new(os.getenv("HOME") .. "/.hammerspoon/", reload_config):start()
```

There are several things worth breaking down about this example. Firstly, we're using a Lua function called `os.getenv()` to fetch the `HOME` variable from your system's environment. This will tell us where your home directory is. We then use Lua's `..` operator to join that string to the part of the config file's path that we do know, the `/.hammerspoon/` part. This gives us the full path of Hammerspoon's configuration directory.

We then create a new path watcher using this path, and tell it to call our `reload_config` function whenever something changes in the `.hammerspoon` directory. We then immediately call `start()` on the path watcher object, so it begins its work.

Note that we didn't have to use a separate function to pass to `hs.pathwatcher.new()`, we're just doing that to show how it can be done if you want to keep things a little more separated, or be able to re-use functions in multiple places.

### <a name="appmenus"></a>Interacting with application menus

Sometimes the only way to automate something is to interact with the GUI of an application, which is not ideal, but is often necessary to get something done.

To illustrate this, we're going to build a hotkey that cycles Safari between multiple User Agent strings (i.e. how it identifies itself to web servers). To do this, you'll need to have the Safari `Develop` menu enabled, which you can do by ticking `Show Develop menu in menu bar` in `Preferences→Advanced`.

```lua
function cycle_safari_agents()
    hs.application.launchorfocus("Safari")
    local safari = hs.appfinder.app_from_name("Safari")

    local str_default = {"Develop", "User Agent", "Default (Automatically Chosen)"}
    local str_ie10 = {"Develop", "User Agent", "Internet Explorer 10.0"}
    local str_chrome = {"Develop", "User Agent", "Google Chrome — Windows"}

    local default = safari:findmenuitem(str_default)
    local ie10 = safari:findmenuitem(str_ie10)
    local chrome = safari:findmenuitem(str_chrome)

    if (default and default["ticked"]) then
        safari:selectmenuitem(str_ie10)
        hs.alert.show("IE10")
    end
    if (ie10 and ie10["ticked"]) then
        safari:selectmenuitem(str_chrome)
        hs.alert.show("Chrome")
    end
    if (chrome and chrome["ticked"]) then
        safari:selectmenuitem(str_default)
        hs.alert.show("Safari")
    end
end
hs.hotkey.bind({"cmd", "alt", "ctrl"}, '7', cycle_safari_agents)
```

What we are doing here is first launching Safari or bringing it to the front if it is already running. This is an important step in any menu interaction - menus for apps that are not currently focused, will usually be disabled.

We then get a reference to Safari itself using hs.appfinder.app_from_name(). Using this object we can search the available menu items and interact with them. Specifically, we are looking for the current state of three of the User Agent strings in `Develop→User Agent`. We then check to see which of them is ticked, and then select the next one.

Thus, pressing `cmd+alt+ctrl+7` repeatedly will cycle between the default user agent string, an IE10 user agent, and a Chrome user agent. Each time, we display a simple on-screen alert with the name of the user agent we have cycled to.

# Credits

This guide owes a huge debt to Joseph Holsten and his [Mjolnir guide](http://blog.josephholsten.com/post/scripting-your-mac-getting-started)
