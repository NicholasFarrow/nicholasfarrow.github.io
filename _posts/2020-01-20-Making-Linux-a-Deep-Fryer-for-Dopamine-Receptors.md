---
layout: posts
title: "Making Linux a Deep Fryer for Dopamine Receptors"
author_profile: true
last_modified_at: 2020-01-20T03:20:02-05:00
date: 2020-01-20
header:
  teaser: "/assets/images/blurHeader.png"
  og_image: "/assets/images/blurHeader.png"
layout: single
classes: wide
author_profile: true
read_time: true
comments: # true
share: true
related: true
toc: true
toc_label: "Software"
comments: true
---
My current Linux setup (i3-gaps, compton, wal, st, + more).

![desktop](/assets/images/deepfried.png)

> **Make each program do one thing well.** *To do a new job, build afresh rather than complicate old programs by adding new "features".*	--[Douglas McIlroy, Bell Labs (1978)](https://en.wikipedia.org/wiki/Unix_philosophy)

Early last year I began to properly explore Linux. Motivated by beautiful desktops, and customisation made easy thanks to the UNIX philosophy above, I have gradually synthesised an aesthetic and productive workflow on my systems. I haven't really had a reason to use Windows since.

The following setup definitely works on Ubuntu, but it and other distros may require alternate installation proceedures.

# i3-gaps (tiling window manager)
`i3-gaps` is a fork of the tiling window manager `i3`. Compared to standard window managers, tiling allows for efficient maneuvering of split windows and is almost entirely controlled via the keyboard.

The 'gaps' means that tiled windows can have controllable distance between them, as well as a gap border surrounding the outside of the tiles. I personally use [rounded i3-gaps](https://github.com/resloved/i3) which has nice rounded corners. 

You can install rounded i3-gaps with
~~~shell
git clone git clone https://github.com/resloved/i3 i3-gaps
cd i3-gaps/
make
sudo make install
~~~
Then add `exec i3` to your `~/.xinitrc`.

[See here](https://www.youtube.com/watch?v=GKviflL9XeI) for a good introductory video on navigating `i3` using the keyboard. Also, see my [i3 config](https://github.com/NicholasFarrow/plugfiles/blob/arch-x1/.config/i3/config) which you should install in `~/.config/i3/config` and customise.

# Compton (transparency & blur)
Compton is a compositor which composits windows into an offscreen buffer before writing to the display memory. This allows for numerous effects, particularly transparency and blurring.

I use [tryone's compton fork](https://github.com/tryone144/compton) which includes a visually pleasing *kawase blur*, making text easier to read on transparent windows.

![compton blur comparison](/assets/images/blurComparison.png)

You can install compton with:
~~~shell
git clone https://github.com/tryone144/compton
cd compton
make
make install
~~~
and I run on startup via my `i3` config using `exec_always --no-startup-id compton --blur-background --blur-method kawase --blur-strength 8 --opacity-rule 30:'class_g="st"' --backend glx`.

Checkout `~/.config/compton.conf` for endless configuration. For `i3` I like setting `inactive-opacity = 0.95;` so that **EVERY** window will become very slightly transparent when innactive. This makes it easier to observe the active window.

# wal (change background and colours)
`wal -i ~/Pictures/Wallpapers/ --saturate 0.3 -l` is likely my most used command (aliased to `cbs`). It chooses a random desktop background from a folder, samples a colour palette from the dominant colours in the image, and then applies the colours system-wide. I have found that this saturation and `-l` for lightmode creates the best colour palette with high text readability.

![wal gif](/assets/images/waldopamine.gif)

# polybar
For a status bar I am now using `polybar`, which takes on the system colours from `wal`! Not only does it look much prettier than `i3status` but it comes with some nice modular functions, while retaining the ability to include output custom scripts.

Below is an image of my polybar. Left: custom crypto price tickers and VPN status. Right: volume, RAM usage, CPU usage, internet connection, CPU temperature, time (shows date when clicked), and a power button I have never actually used.

![polybar](/assets/images/polybar.png)

You can find my [polybar config here](https://github.com/NicholasFarrow/plugfiles/blob/arch-x1/.config/polybar/config).

# st (terminal)
I am using [Luke Smith's fork](https://github.com/LukeSmithxyz/st) of the suckless simple terminal (st) which has some really nice features such as scrollback, font-size hotkeys, good text copy/paste.

To install:
~~~shell
git clone https://github.com/LukeSmithxyz/st
cd st
sudo make install
~~~

Message me if you need any help with any of the above.
