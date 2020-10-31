---
title: "Misc"
toc: true
#toc_label: "Unique Title"
#toc_icon: "heart"
layout: posts
author_profile: true
permalink: /Misc/
---
# UNIX
All of the mentioned `packages` can be downloaded using `sudo apt-get install PACKAGE`.
## Music in terminal
Using `mpv` and `youtube-dl` we can stream a single song using
```shell
mpv https://youtu.be/JUpidCc7wwY --no-video
```
or shuffle a whole playlist
```shell
mpv PLAYLIST_LINK --shuffle --no-video
```

We can create a audio visual using `vis` in another terminal
<img src="/assets/images/Misc/audioVisual.png" alt="Visual example" width="70%"/>

## Random wallpaper from folder
```shell
feh --randomize --bg-fill ~/Pictures/Wallpapers/*
```

## Multiscreen Streaming 
Automatically switch OBS streaming scene to desktop with current mouse location:

```shell
#!/bin/bash
# Whenever out mouse is past XSPLIT in the X-direction,
# A hotkey is sent to OBS to change scenes
XSPLIT=1920

# Hotkeys
LEFT='F7'
RIGHT='F8'

PAST=$RIGHT

obs | while :
do
	XCOORD=$(xdotool getmouselocation | cut -d : -f 2 | cut -d ' ' -f 1)

	if [ "$XCOORD" -lt "$XSPLIT" ] && [ "$PAST" == "$RIGHT" ]; then
		WINDOWID=$(xdotool search --name OBS | tail -1)
		xdotool key --window $WINDOWID $LEFT
		PAST=$LEFT
		echo "CHANGE LEFT"

	elif [ "$XCOORD" -gt "$XSPLIT" ] && [ "$PAST" == "$LEFT" ]
	then
		WINDOWID=$(xdotool search --name OBS | tail -1)
		xdotool key --window $WINDOWID $RIGHT
		PAST=$RIGHT
		echo "CHANGE RIGHT"
	fi
done
```
