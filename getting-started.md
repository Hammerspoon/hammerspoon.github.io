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
 * [Multi-window layouts](#winlayout)
 * [Simple config reloading](#simplereload)
 * [Fancy config reloading](#fancyreload)
 * [Interacting with application menus](#appmenus)
 * [Reacting to application events](#appevents)
 * [Creating a simple menubar item](#simplemenubar)
 * [Reacting to wifi events](#wifievents)

### <a name="helloworld"></a>Hello World

All good programming tutorials start with a Hello World example of some kind, so we'll use Hammerspoon's ability to bind keyboard hotkeys to demonstrate saying Hello World with a simple notification.

In your `init.lua` place the following:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "W", function()
  hs.alert.show("Hello World!")
end)
```

Then click on the Hammerspoon menubar icon and click `Reload Config`. You should now find that pressing <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>ctrl</kbd>+<kbd>W</kbd> will display a Hello World notification on your screen.

What is happening here is that we're telling Hammerspoon to bind an anonymous function to a particular hotkey. The hotkey is specified by a table of modifier keys (<kbd>⌘</kbd>, <kbd>⌥</kbd> and <kbd>ctrl</kbd> in this case) and a normal key (<kbd>W</kbd>). An anonymous function is simply one that doesn't have a name. We could have defined the alert function separately with a name and passed that name to `hs.hotkey.bind()`, but Lua makes it easy to define the functions inline.

### <a name="winmoveintro"></a>Introduction to window movement

One of the most useful things you can do with Hammerspoon is to manipulate the windows on your screen. We'll start off with a simple example and build up to something more complicated.

Add the following to your `init.lua`:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x - 10
  win:setFrame(f)
end)
```

This will now cause <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>ctrl</kbd>+<kbd>H</kbd> to make move the currently focused window 10 pixels to the left. You can see that we fetch the currently focused window and then obtain its frame. This describes the location and size of the window. We can then modify the frame and apply it back to the window using `setFrame()`.

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
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x - 10
  f.y = f.y - 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "K", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.y = f.y - 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "U", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x + 10
  f.y = f.y - 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "H", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x - 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "L", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x + 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "B", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x - 10
  f.y = f.y + 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "J", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.y = f.y + 10
  win:setFrame(f)
end)

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "N", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()

  f.x = f.x + 10
  f.y = f.y + 10
  win:setFrame(f)
end)
```

Try it out!

### <a name="winresize"></a>Window sizing

In this section we'll implement the common window management feature of moving a window so it occupies either the left or right half of the screen, allowing you to tile two windows next to each other for Productivity™.

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Left", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.x = max.x
  f.y = max.y
  f.w = max.w / 2
  f.h = max.h
  win:setFrame(f)
end)
```

Here we are binding <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>ctrl</kbd>+<kbd>←</kbd> (as in the left cursor key) to a function that will fetch the focused window, then fetch the screen that the focused window is on, fetch the frame of the screen (note that `hs.screen.frame()` does not include the menubar and dock, see `hs.screen.fullframe()` if you need that) and set the frame of the window to occupy the left half of the screen.

To round that out, we'll add a function to move the window to the right half of the screen:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "Right", function()
  local win = hs.window.focusedWindow()
  local f = win:frame()
  local screen = win:screen()
  local max = screen:frame()

  f.x = max.x + (max.w / 2)
  f.y = max.y
  f.w = max.w / 2
  f.h = max.h
  win:setFrame(f)
end)
```

A good exercise here would be to see if you can now write functions for yourself that bind the Up/Down cursor keys to resizing windows to the top/bottom half of the screen, respectively.

### <a name="winlayout"></a>Multi-window layouts

When you want to keep several apps open all the time, and have their windows arranged in a particular way, you can use the `hs.layout` extension:

```lua
    local laptopScreen = "Color LCD"
    local windowLayout = {
        {"Safari",  nil,          laptopScreen, hs.layout.left50,    nil, nil},
        {"Mail",    nil,          laptopScreen, hs.layout.right50,   nil, nil},
        {"iTunes",  "iTunes",     laptopScreen, hs.layout.maximized, nil, nil},
        {"iTunes",  "MiniPlayer", laptopScreen, nil, nil, hs.geometry.rect(0, -48, 400, 48)},
    }
    hs.layout.apply(windowLayout)
```

To break this down a little, we start off by creating a variable with the name of the main screen on a Mac. You can find these names with the `:name()` method on an `hs.screen` object (e.g. typing `hs.screen.allScreens()[1]:name()` in the Hammerspoon Console).

We then create a table that describes the layout we want. Each entry in the `windowLayout` table is another table that selects the windows we are interested in, and specifies their desired position and size.

The first item in the table is the name of an app we wish to affect, and the second item is the title of a window we wish to affect. Either of these items can be `nil`, but not both. If the application name is `nil` then we will match the given window title across all applications. If the window title item is `nil` then we will match all windows of the given application.

The third item is the name of the screen to place the window on, as described above.

The fourth, fifth and sixth items are used to describe the layout of matched windows, in different ways. Only one of these items can have a value, and that value should be a table containing four items, `x`, `y`, `w` and `h` (horizontal position, vertical position, width and height, respectively).

The fourth item is a rect that will be given to `hs.window:moveToUnit()`. The `x`, `y`, `w`, and `h` values of this rect, are values between `0.0` and `1.0`, allowing you to position windows as fractions of the display, without having to be concerned about the precise resolution of the display (e.g. `hs.layout.left50` is a pre-defined rect of `hs.geometry.rect(0, 0, 0.5, 1)`).

The fifth item is a rect that will be given to `hs.window:setFrame()` and should specify the position/size values as pixel positions on the screen, but without the OS menubar and dock.

The sixth item is similar to the fifth, except it does take the OS menubar and dock into account. This is shown in our example above, which will place the iTunes Mini Player window at the very bottom left of the screen, even if the dock is there.

This may seem like a fairly complex set of options, but it's worth spending some time learning, as it allows for extremely powerful window layouts, particularly in reaction to system events (such as the number of screens changing when you plug in a monitor, or even just press a particular hotkey to restore sanity to your windows).

### <a name="simplereload"></a>Simple configuration reloading

You may have noticed that while you're editing the config, it's a little bit annoying to have to keep choosing the `Reload Config` menu item every time you make a change. We can fix that by adding a keyboard shortcut to reload the config:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "R", function()
  hs.reload()
end)
hs.alert.show("Config loaded")
```

We've now bound `cmd+alt+ctrl+R` to a function that will reload the config and display a simple alert banner on the screen for a couple of seconds.

One important detail to call out here is that `hs.reload()` destroys the current Lua interpreter and creates a new one. If we had any code after `hs.reload()` in this function, it would not be called.

### <a name="fancyreload"></a>Fancy configuration reloading

So we can now manually force a reload, but why should we even have to do that when the computer could do it for us‽

The following snippet introduces another new extension, `pathwatcher` which will allow us to automatically reload the config whenever the file changes:

```lua
function reload_config(files)
    hs.reload()
end
hs.pathwatcher.new(os.getenv("HOME") .. "/.hammerspoon/", reload_config):start()
hs.alert.show("Config loaded")
```

There are several things worth breaking down about this example. Firstly, we're using a Lua function called `os.getenv()` to fetch the `HOME` variable from your system's environment. This will tell us where your home directory is. We then use Lua's `..` operator to join that string to the part of the config file's path that we do know, the `/.hammerspoon/` part. This gives us the full path of Hammerspoon's configuration directory.

We then create a new path watcher using this path, and tell it to call our `reload_config` function whenever something changes in the `.hammerspoon` directory. We then immediately call `start()` on the path watcher object, so it begins its work.

In this example we've implemented the config reloading function as a separate, named function, which we pass as an argument to `hs.pathwatcher.new()`. It's entirely up to you whether you pass around named functions, or use anonymous ones in-line.

### <a name="appmenus"></a>Interacting with application menus

Sometimes the only way to automate something is to interact with the GUI of an application, which is not ideal, but is often necessary to get something done.

To illustrate this, we're going to build a hotkey that cycles Safari between multiple User Agent strings (i.e. how it identifies itself to web servers). To do this, you'll need to have the Safari `Develop` menu enabled, which you can do by ticking `Show Develop menu in menu bar` in `Safari→Preferences→Advanced`.

```lua
function cycle_safari_agents()
    hs.application.launchOrFocus("Safari")
    local safari = hs.appfinder.appFromName("Safari")

    local str_default = {"Develop", "User Agent", "Default (Automatically Chosen)"}
    local str_ie10 = {"Develop", "User Agent", "Internet Explorer 10.0"}
    local str_chrome = {"Develop", "User Agent", "Google Chrome — Windows"}

    local default = safari:findMenuItem(str_default)
    local ie10 = safari:findMenuItem(str_ie10)
    local chrome = safari:findMenuItem(str_chrome)

    if (default and default["ticked"]) then
        safari:selectMenuItem(str_ie10)
        hs.alert.show("IE10")
    end
    if (ie10 and ie10["ticked"]) then
        safari:selectMenuItem(str_chrome)
        hs.alert.show("Chrome")
    end
    if (chrome and chrome["ticked"]) then
        safari:selectMenuItem(str_default)
        hs.alert.show("Safari")
    end
end
hs.hotkey.bind({"cmd", "alt", "ctrl"}, '7', cycle_safari_agents)
```

What we are doing here is first launching Safari or bringing it to the front if it is already running. This is an important step in any menu interaction - menus for apps that are not currently focused, will usually be disabled.

We then get a reference to Safari itself using `hs.appfinder.appFromName()`. Using this object we can search the available menu items and interact with them. Specifically, we are looking for the current state of three of the User Agent strings in `Develop→User Agent`. We then check to see which of them is ticked, and then select the next one.

Thus, pressing `cmd+alt+ctrl+7` repeatedly will cycle between the default user agent string, an IE10 user agent, and a Chrome user agent. Each time, we display a simple on-screen alert with the name of the user agent we have cycled to.

### <a name="appevents"></a>Reacting to application events

Using the ```hs.application.watcher``` callback we can react to various application level events, such as applications being launched, exiting, hiding, and activating.

We can demonstrate this by creating a very simple callback which will make sure that when you activate the Finder application, all of its windows will be brought to the front of the display.

```lua
function applicationWatcher(appName, eventType, appObject)
    if (eventType == hs.application.watcher.activated) then
        if (appName == "Finder") then
            -- Bring all Finder windows forward when one gets activated
            appObject:selectMenuItem({"Window", "Bring All to Front"})
        end
    end
end
local appWatcher = hs.application.watcher.new(applicationWatcher)
appWatcher:start()
```

To start with, we define a callback function which accepts three parameters and in it we check if the type of event that triggers the function, is an application being activated. Then we check if the application being activated is Finder. If it is, we select its menu item to bring all of its windows to the front.

We then create an application watcher object that will call our function, and tell it to start.

Note that we kept a reference to the watcher object, rather than simply calling ```hs.application.watcher.new(applicationWatcher):start()```. The reason for this is so that we can call ```:stop()``` on the watcher later if we need to (for example in a function that reloads our config - see the Fancy Config Reloading example for information on how to reload Hammerspoon's configuration automatically).

### <a name="simplemenubar"></a>Creating a simple menubar item

Lots of Mac utilities place a small icon in the system menubar to display their status and let you interact with them. We're going to use two of Hammerspoon's extensions to whip up a very simple replacement for the popular utility `Caffeine`.

```lua
-- NOTE: If you have a function set up to reload your config, you should call caffeine:delete() there
local caffeine = hs.menubar.new()
function setCaffeineDisplay(state)
    if state then
        caffeine:setTitle("AWAKE")
    else
        caffeine:setTitle("SLEEPY")
    end
end

function caffeineClicked()
    setCaffeineDisplay(hs.caffeinate.toggle("displayIdle"))
end

if caffeine then
    caffeine:setClickCallback(caffeineClicked)
    setCaffeineDisplay(hs.caffeinate.get("displayIdle"))
end
```

This code snippet will create a menubar item that displays either the text `SLEEPY` if your machine is allowed to go to sleep when you're not using it, or `AWAKE` if it will refuse to sleep. The `hs.caffeine` extension provides the ability to prevent the display from sleeping, but `hs.menubar` is providing the menubar item.

In this case we create the menubar item and connect a callback (in this case `caffeineClicked()`) to click events on the menubar item. You can also use icons instead of text, by placing small image files in `~/.hammerspoon/` and using the `:setIcon()` method on your menubar object. See the full API docs for `hs.menubar` for more information about this.

### <a name="wifievents"></a>Reacting to wifi events

If you use a MacBook then you probably have a WiFi network at home. It's very simple with Hammerspoon to trigger events when you are either arriving home and joining your WiFi network, or departing home and leaving the network. In this case we'll do something simple and adjust the audio volume of the MacBook such that it's at zero when you're away from home (protecting you from the shame of opening your MacBook in a coffee shop and blaring out the music you had playing at home!)

```lua
local wifiWatcher = nil
local homeSSID = "MyHomeNetwork"
local lastSSID = hs.wifi.currentNetwork()

function ssidChangedCallback()
    newSSID = hs.wifi.currentNetwork()

    if newSSID == homeSSID and lastSSID ~= homeSSID then
        -- We just joined our home WiFi network
        hs.audiodevice.defaultOutputDevice():setVolume(25)
    elseif newSSID ~= homeSSID and lastSSID == homeSSID then
        -- We just departed our home WiFi network
        hs.audiodevice.defaultOutputDevice():setVolume(0)
    end

    lastSSID = newSSID
end

-- NOTE: If you have a function set up to reload your config, you should call wifiWatcher:stop() there
wifiWatcher = hs.wifi.watcher.new(ssidChangedCallback)
wifiWatcher:start()
```

Here we have created a callback function that compares the current WiFi network's name to the previous network's name and examines whether we have moved from our pre-defined home network to something else, or vice versa, and then uses `hs.audiodevice` to adjust the system volume.

# Credits

This guide owes a huge debt to Joseph Holsten and his [Mjolnir guide](http://blog.josephholsten.com/post/scripting-your-mac-getting-started)
