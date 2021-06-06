---
layout: default
title: Getting Started
permalink: /go/
---

# Getting Started with Hammerspoon

## What is Hammerspoon?

Hammerspoon is a desktop automation tool for OS X. It bridges various system level APIs into a Lua scripting engine, allowing you to have powerful effects on your system by writing Lua scripts.

## What is Lua?

Lua is a simple programming language. If you have never programmed in Lua before, you may want to run through [Learn Lua in Y minutes](http://learnxinyminutes.com/docs/lua/) before you begin.

## Setup

 * [Download the latest release of Hammerspoon](https://github.com/Hammerspoon/hammerspoon/releases/latest) and drag it to your `/Applications` folder
 * Run Hammerspoon.app and follow the prompts to enable Accessibility access for the app
 * Click on the Hammerspoon menu bar icon and choose `Open Config` from the menu
 * Open the Hammerspoon [API docs](http://www.hammerspoon.org/docs/) in your browser, to explore the extensions we provide, and the functions they offer

## Table of Contents

 * [Hello World](#helloworld)
 * [Fancier Hello World](#fancyhelloworld)
 * [Spoons](#spoons)
 * [Introduction to window movement](#winmoveintro)
 * [A quick aside on colon syntax](#colonsyntax)
 * [A quick aside on variable lifecycles](#variablelife)
 * [More complex window movement](#winmovenethack)
 * [Window resizing](#winresize)
 * [Multi-window layouts](#winlayout)
 * [Window filters](#winfilters)
 * [Simple config reloading](#simplereload)
 * [Fancy config reloading](#fancyreload)
 * [Smart config reloading with Spoons](#smartreload)
 * [Interacting with application menus](#appmenus)
 * [Creating a simple menubar item](#simplemenubar)
 * [Reacting to application events](#appevents)
 * [Reacting to wifi events](#wifievents)
 * [Reacting to USB events](#usbevents)
 * [Defeating paste-blocking](#pasteblock)
 * [Running AppleScript](#applescript)
 * [Controlling iTunes/Spotify](#itunesspotify)
 * [Drawing on the screen](#drawing)
 * [Sending iMessage/SMS messages](#imessagesms)
 * [Automating Hammerspoon with URLs](#ipcurl)

### <a name="helloworld"></a>Hello World

All good programming tutorials start with a Hello World example of some kind, so we will use Hammerspoon's ability to bind keyboard hotkeys to demonstrate saying Hello World with a simple notification.

In your `init.lua` place the following:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "W", function()
  hs.alert.show("Hello World!")
end)
```

Then save the file, click on the Hammerspoon menubar icon and choose `Reload Config`. You should now find that pressing <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>ctrl</kbd>+<kbd>W</kbd> will display a Hello World notification on your screen.

What is happening here is that we're telling Hammerspoon to bind an anonymous function to a particular hotkey. The hotkey is specified by a table of modifier keys (<kbd>⌘</kbd>, <kbd>⌥</kbd> and <kbd>ctrl</kbd> in this case) and a normal key (<kbd>W</kbd>). An anonymous function is simply one that doesn't have a name. We could have defined the alert function separately with a name and passed that name to `hs.hotkey.bind()`, but Lua makes it easy to define the functions inline.

### <a name="fancyhelloworld"></a>Fancier Hello World

While `hs.alert` is useful, you might prefer to use the OS X native notifications instead, which you can do by simply modifying the previous example to:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "W", function()
  hs.notify.new({title="Hammerspoon", informativeText="Hello World"}):send()
end)
```

### <a name="spoons"></a>Spoons

Spoons are pre-made plugins for Hammerspoon.

Have a look at the official documentation for Spoons
[here](https://github.com/Hammerspoon/hammerspoon/blob/master/SPOONS.md)
about how they work and how to use them.

The official website with the listing of Spoons is
[https://www.hammerspoon.org/Spoons/](https://www.hammerspoon.org/Spoons/)
and the official repository is
[https://github.com/Hammerspoon/Spoons](https://github.com/Hammerspoon/Spoons).


### <a name="winmoveintro"></a>Introduction to window movement

One of the most immediately useful things you can do with Hammerspoon is to manipulate the windows on your screen. We'll start off with a simple example and build up to something more complicated.

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

You might have noticed that sometimes we're using dots in function calls, and sometimes we're using colons. The colon syntax means you're calling one of that object's methods. It's still a function call, but it implicitly passes the object to the method as a `self` argument.

### <a name="variablelife"></a>A quick aside about variable lifecycles

Lua uses Garbage Collection to clean up its memory usage - any object it believes is no longer in use, will be destroyed at some point in the future (exactly when, can be very unpredictable, but it's based around how active your Lua code is).

This means that a variable which only exists inside a function/loop/etc will be available for garbage collection as soon as the function/loop has finished executing. This includes your `init.lua`, which is considered to be a single scope that finishes when the final line of code has run.

If you create any objects in your `init.lua`, you must capture them in a variable, or they will be silently destroyed at some point in the future. For example:

```lua
hs.pathwatcher.new(.....):start()
```

The object returned here, an `hs.pathwatcher` object, is not being captured, so it is available for Garbage Collection as soon as your init.lua is finished. It will likely not be destroyed for some minutes/hours after, but you will then be confused why your pathwatcher is not running. Instead, this version will survive for until you reload your config, or quit Hammerspoon:

```lua
myWatcher = hs.pathwatcher.new(.....):start()
```

The `myWatcher` variable is a global variable, so will never go out of scope.

As a further aside about the lifecycle of variables - in the Console window, each time you type a line and hit enter, a distinct Lua scope is created, executed and finished. This means that `local` variables created in the Console window will immediately become inaccessible when you hit Enter, because their scope has closed.

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

Here we are binding <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>ctrl</kbd>+<kbd>←</kbd> (as in the left cursor key) to a function that will fetch the focused window, then fetch the screen that the focused window is on, fetch the frame of the screen (note that `hs.screen.frame()` does not include the menubar and dock, see `hs.screen.fullFrame()` if you need that) and set the frame of the window to occupy the left half of the screen.

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

The third item is the name of the screen to place the window on, as described above (see the [API docs](http://www.hammerspoon.org/docs/) for more ways to specify the screen).

The fourth, fifth and sixth items are used to describe the layout of matched windows, in different ways. Only one of these items can have a value, and that value should be a table containing four items, `x`, `y`, `w` and `h` (horizontal position, vertical position, width and height, respectively).

The fourth item is a rect that will be given to `hs.window:moveToUnit()`. The `x`, `y`, `w`, and `h` values of this rect, are values between `0.0` and `1.0`, allowing you to position windows as fractions of the display, without having to be concerned about the precise resolution of the display (e.g. `hs.layout.left50` is a pre-defined rect of `{x=0, y=0, w=0.5, h=1}`).

The fifth item is a rect that will be given to `hs.window:setFrame()` and should specify the position/size values as pixel positions on the screen, but without the OS menubar and dock taken into account.

The sixth item is similar to the fifth, except it does take the OS menubar and dock into account. This is shown in our example above, which will place the iTunes Mini Player window at the very bottom left of the screen, even if the dock is there. Note that we're using the `hs.geometry.rect()` helper function to construct the rect table and that the `y` value is negative, meaning that the top of the window should start 48 pixels above the bottom of the display.

This may seem like a fairly complex set of options, but it's worth spending some time learning, as it allows for extremely powerful window layouts, particularly in reaction to system events (such as the number of screens changing when you plug in a monitor, or even just press a particular hotkey to restore sanity to your windows).

### <a name="winfilters"></a>Window filters

Wouldn't it be useful to have hotkeys bound in certain contexts or applications but not others? Organize windows and react to events on the basis of position, size, workflow, or any combination thereof? The extremely versatile `hs.window.filter` module allows this, enabling you to create complex window groupings and behaviors with filtering rules and event watchers. The best way to demonstrate the power of this module is through examples.

When copying and pasting content from Safari to Messages.app, all links are jarringly expanded, making the text hard to read:

`Thrushes make up the Turdidae, a family <https://en.wikipedia.org/wiki/Family_(biology)> of passerine <https://en.wikipedia.org/wiki/Passerine> birds <https://en.wikipedia.org/wiki/Bird> that occurs worldwide.`

Only the Safari/Messages pair suffers from this and other OS X applications generally copy and paste without surprises. This annoyance is cleanly rectified with the help of a windowfilter:

```lua
local function cleanPasteboard()
  local pb = hs.pasteboard.contentTypes()
  local contains = hs.fnutils.contains
  if contains(pb, "com.apple.webarchive") and contains(pb, "public.rtf") then
    hs.pasteboard.setContents(hs.pasteboard.getContents())
  end
end

local messagesWindowFilter = hs.window.filter.new(false):setAppFilter('Messages')
messagesWindowFilter:subscribe(hs.window.filter.windowFocused, cleanPasteboard)
```

The `cleanPasteboard` function replaces the Safari 'rich text' on the pasteboard with plain text after checking the pasteboard content metadata types. This ensures that copying and pasting of images from Safari still works. An empty windowfilter is created by initializing with `false` to excludes all windows by default. A Messages 'appfilter' is added so that this windowfilter only observes Messages windows. We then subscribe to the windowfilter so that `cleanPasteboard` is called each time a Messages window gains focus. You can similarly enable/disable custom hotkeys when certain windows or applications have focus.

Windowfilters are dynamic, filtering automatically in the background according to the constraints set. By initializing a windowfilter with a predicate function, you can create arbitrarily complex filtering rules:

```lua
local wf = hs.window.filter.new(function(win)
    local fw = hs.window.focusedWindow()
    return (
      win:isStandard() and
      win:application() == fw:application() and
      win:screen() == fw:screen()
    )
  end)
```

This windowfilter contains all standard (unhidden, non-modal) windows that share both the application and screen of the currently focused window. The windowfilter continually updates so that the currently focused window determines the set of windows in play. This can be used to cycle through focused application windows on the current screen using `hs.window.switcher` or your own custom cycler.

### <a name="simplereload"></a>Simple configuration reloading

You may have noticed that while you're editing the config, it's a little bit annoying to have to keep choosing the `Reload Config` menu item every time you make a change. We can fix that by adding a keyboard shortcut to reload the config:

```lua
hs.hotkey.bind({"cmd", "alt", "ctrl"}, "R", function()
  hs.reload()
end)
hs.alert.show("Config loaded")
```

We have now bound <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>⌃</kbd>+<kbd>R</kbd> to a function that will reload the config and display a simple alert banner on the screen for a couple of seconds.

One important detail to call out here is that `hs.reload()` destroys the current Lua interpreter and creates a new one. If we had any code after `hs.reload()` in this function, it would not be called.

### <a name="fancyreload"></a>Fancy configuration reloading

So we can now manually force a reload, but why should we even have to do that when the computer could do it for us‽

The following snippet introduces another new extension, `pathwatcher` which will allow us to automatically reload the config whenever the file changes:

```lua
function reloadConfig(files)
    doReload = false
    for _,file in pairs(files) do
        if file:sub(-4) == ".lua" then
            doReload = true
        end
    end
    if doReload then
        hs.reload()
    end
end
myWatcher = hs.pathwatcher.new(os.getenv("HOME") .. "/.hammerspoon/", reloadConfig):start()
hs.alert.show("Config loaded")
```

There are several things worth breaking down about this example. Firstly, we're using a Lua function called `os.getenv()` to fetch the `HOME` variable from your system's environment. This will tell us where your home directory is. We then use Lua's `..` operator to join that string to the part of the config file's path that we do know, the `/.hammerspoon/` part. This gives us the full path of Hammerspoon's configuration directory.

We then create a new path watcher using this path, and tell it to call our `reloadConfig` function whenever something changes in the `.hammerspoon` directory. We then immediately call `start()` on the path watcher object, so it begins its work.

In this example we've implemented the config reloading function as a separate, named function, which we pass as an argument to `hs.pathwatcher.new()`. It's entirely up to you whether you pass around named functions, or use anonymous ones in-line.

This function accepts a single argument, which is a table containing all the names of files that have been modifier. It iterates over that list and checks each file to see if it ends with `.lua`. If any Lua files have been changed, it then tells Hammerspoon to destroy the current Lua setup and reload its configuration files.

### <a name="smartreload"></a>Smart configuration reloading with Spoons

Hammerspoon supports Lua plugins that we call "Spoons". They allow anyone to build useful functionality with Hammerspoon's APIs and then distribute that to other people.

Since configuration reloading is something that many users are likely to want, it's an ideal candidate for a Spoon, and one exists in the official Spoons repository [here](http://www.hammerspoon.org/Spoons/ReloadConfiguration.html).

To start with, click on the Download link on the Spoon's webpage - this should download the Zip file and extract it to your `Downloads` folder, where it will appear with a spoon icon. Open that file and Hammerspoon will automatically import the Spoon to `~/.hammerspoon/Spoons/`.

Then add the following to your `init.lua` and you're done:

```lua
hs.loadSpoon("ReloadConfiguration")
spoon.ReloadConfiguration:start()
```

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

Thus, pressing <kbd>⌘</kbd>+<kbd>⌥</kbd>+<kbd>⌃</kbd>+<kbd>7</kbd> repeatedly will cycle between the default user agent string, an IE10 user agent, and a Chrome user agent. Each time, we display a simple on-screen alert with the name of the user agent we have cycled to.

### <a name="simplemenubar"></a>Creating a simple menubar item

Lots of Mac utilities place a small icon in the system menubar to display their status and let you interact with them. We're going to use two of Hammerspoon's extensions to whip up a very simple replacement for the popular utility `Caffeine`.

```lua
caffeine = hs.menubar.new()
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
appWatcher = hs.application.watcher.new(applicationWatcher)
appWatcher:start()
```

To start with, we define a callback function which accepts three parameters and in it we check if the type of event that triggers the function, is an application being activated. Then we check if the application being activated is Finder. If it is, we select its menu item to bring all of its windows to the front.

We then create an application watcher object that will call our function, and tell it to start.

Note that we kept a reference to the watcher object, rather than simply calling ```hs.application.watcher.new(applicationWatcher):start()```. The reason for this is so that we can call ```:stop()``` on the watcher later if we need to (for example in a function that reloads our config - see the Fancy Config Reloading example for information on how to reload Hammerspoon's configuration automatically).

### <a name="wifievents"></a>Reacting to wifi events

If you use a MacBook then you probably have a WiFi network at home. It's very simple with Hammerspoon to trigger events when you are either arriving home and joining your WiFi network, or departing home and leaving the network. In this case we'll do something simple and adjust the audio volume of the MacBook such that it's at zero when you're away from home (protecting you from the shame of opening your MacBook in a coffee shop and blaring out the music you had playing at home!)

```lua
wifiWatcher = nil
homeSSID = "MyHomeNetwork"
lastSSID = hs.wifi.currentNetwork()

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

wifiWatcher = hs.wifi.watcher.new(ssidChangedCallback)
wifiWatcher:start()
```

Here we have created a callback function that compares the current WiFi network's name to the previous network's name and examines whether we have moved from our pre-defined home network to something else, or vice versa, and then uses `hs.audiodevice` to adjust the system volume.

### <a name="usbevents"></a>Reacting to USB events

If you have a piece of USB hardware that you want to be able to react to, `hs.usb.watcher` is the extension for you. In the example below, we'll automatically start the software for a scanner when it is plugged in, and then kill the software when the scanner is unplugged.

```lua
usbWatcher = nil

function usbDeviceCallback(data)
    if (data["productName"] == "ScanSnap S1300i") then
        if (data["eventType"] == "added") then
            hs.application.launchOrFocus("ScanSnap Manager")
        elseif (data["eventType"] == "removed") then
            app = hs.appfinder.appFromName("ScanSnap Manager")
            app:kill()
        end
    end
end

usbWatcher = hs.usb.watcher.new(usbDeviceCallback)
usbWatcher:start()
```

### <a name="pasteblock"></a>Defeating paste blocking

You may have noticed that some programs and websites try very hard to stop you from pasting in your password. They seem to think it makes them more secure, but in the age of strongly encrypted password managers, this is, of course, nonsense.

Fortunately, we can route around their damage by emitting fake keyboard events to type the contents of the clipboard:

```lua
hs.hotkey.bind({"cmd", "alt"}, "V", function() hs.eventtap.keyStrokes(hs.pasteboard.getContents()) end)
```

### <a name="applescript"></a>Running AppleScript

Sometimes the automation you need is locked away in an application, which seems like it would be impossible to control from Hammerspoon, except that many applications expose their functionality via AppleScript, which Hammerspoon can execute for you:

```lua
ok,result = hs.applescript('tell Application "iTunes" to artist of the current track as string')
hs.alert.show(result)
```

This will go and ask iTunes for the artist of the track it is currently playing, and then display that on-screen using `hs.alert`.

However, before you rush out and start writing lots of iTunes related AppleScript, check out the next entry in this guide.

### <a name="itunesspotify"></a>Controlling iTunes/Spotify

Using `hs.itunes` and `hs.spotify` we can interrogate/control various aspects of iTunes and Spotify, for example, if you were to need to switch between one app and the other:

```lua
hs.itunes.pause()
hs.spotify.play()
hs.spotify.displayCurrentTrack()
```

### <a name="drawing"></a>Drawing on the screen

Sometimes you just cannot find your mouse pointer. You're sure you left it somewhere, but it's hiding on one of your monitors and wiggling the mouse isn't helping you to spot it. Fortunately, we can interrogate and control the mouse pointer, and we can draw things on the screen, which means we can do something like this:

```lua
mouseCircle = nil
mouseCircleTimer = nil

function mouseHighlight()
    -- Delete an existing highlight if it exists
    if mouseCircle then
        mouseCircle:delete()
        if mouseCircleTimer then
            mouseCircleTimer:stop()
        end
    end
    -- Get the current co-ordinates of the mouse pointer
    mousepoint = hs.mouse.absolutePosition()
    -- Prepare a big red circle around the mouse pointer
    mouseCircle = hs.drawing.circle(hs.geometry.rect(mousepoint.x-40, mousepoint.y-40, 80, 80))
    mouseCircle:setStrokeColor({["red"]=1,["blue"]=0,["green"]=0,["alpha"]=1})
    mouseCircle:setFill(false)
    mouseCircle:setStrokeWidth(5)
    mouseCircle:show()

    -- Set a timer to delete the circle after 3 seconds
    mouseCircleTimer = hs.timer.doAfter(3, function()
      mouseCircle:delete()
      mouseCircle = nil
    end)
end
hs.hotkey.bind({"cmd","alt","shift"}, "D", mouseHighlight)
```

There are several different types of drawing object currently supported - lines, circles, boxes, text and images. Different drawing types can have different properties, which are all fully documented in the API documentation.

Drawing objects can be placed either on top of all other windows, or behind desktop icons - this makes them useful for displaying contextual overlays on top of the screen (such as this mouse finding example), and more permanent information displays behind all the windows (e.g. the kinds of status information people use GeekTool for).

### <a name="imessagesms"></a>Sending iMessage/SMS messages

Rather than explain what this is doing, see if you can figure it out. You may recognise the wifi parts from [Reacting to wifi events](#wifievents):

```lua
coffeeShopWifi = "Baristartisan_Guest"
lastSSID = hs.wifi.currentNetwork()
wifiWatcher = nil

function ssidChanged()
    newSSID = hs.wifi.currentNetwork()

    if newSSID == coffeeShopWifi and lastSSID ~= coffeeShopWifi then
        -- We have arrived at the coffee shop
        hs.messages.iMessage("iphonefriend@hipstermail.com", "Hey! I'm at Baristartisan's, come join me!")
        hs.messages.SMS("+1234567890", "Hey, you don't have an iPhone, but you should still come for a coffee")
    end
end

wifiWatcher = hs.wifi.watcher.new(ssidChanged)
wifiWatcher:start()
```

As you doubtless noticed, this will send two messages to people whenever your Mac arrives at your favourite trendy coffee shop. You'll need to have OS X's Messages app configured and working for sending both iMessages and SMS (the latter via an iPhone using SMS Relay) for this to work.

### <a name="ipcurl"></a>Automating Hammerspoon with URLs

Sometimes you need to automate your automation tools, and Hammerspoon is automatable in several ways. The first way we'll cover here is with URLs. Specifically, URLs that begin with `hammerspoon://`. Given this simple snippet:

```lua
hs.urlevent.bind("someAlert", function(eventName, params)
    hs.alert.show("Received someAlert")
end)
```

We have now bound a URL event handler for an event named `someAlert` that will show a little on-screen text alert. To trigger this event, in a Terminal, run `open -g hammerspoon://someAlert`. Many applications have the ability to open URLs, so this becomes a very simple way to automate Hammerspoon into taking some action. See the next section for a more concrete (and complex) example of this. Note that the `-g` option for `open` causes the URL to be opened in the background, so as to avoid opening Hammerspoon's Console Window, or giving it keyboard focus.

# Credits

This guide owes a huge debt to Joseph Holsten and his [Mjolnir guide](http://blog.josephholsten.com/post/scripting-your-mac-getting-started)
