---
layout: posts
title: "Arch Linux Configuration for Lenovo X1-Extreme"
author_profile: true
last_modified_at: 2019-12-19T03:20:02-05:00
date: 2019-12-19
header:
  teaser: "/assets/images/laptop.jpg"
  og_image: "/assets/images/laptop.jpg"
layout: single
classes: wide
author_profile: true
read_time: true
comments: # true
share: true
related: true
---
I recently installed Arch on my new Lenovo X1 Extreme laptop. Here I document some of the intricacies of the installation. I will continue to update this as I progress with installing more features.

If you see anything wrong, please please tell me!

I followed the [classic archwiki installation guide](https://wiki.archlinux.org/index.php/installation_guide).

## Disk Partitions
~~~shell
nick@graviton:~$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
nvme0n1     259:0    0   477G  0 disk
├─nvme0n1p1 259:1    0   260M  0 part /boot/efi
├─nvme0n1p2 259:2    0    16M  0 part
├─nvme0n1p3 259:3    0 182.7G  0 part
├─nvme0n1p4 259:4    0  1000M  0 part
├─nvme0n1p5 259:5    0    30G  0 part /
├─nvme0n1p6 259:6    0    20G  0 part [SWAP]
└─nvme0n1p7 259:7    0   243G  0 part /home
~~~

I opted to create three linux partitions: 
* One for root (/), 30G for general programs and linux files.
* One swap partition, 20G (~1.5x your RAM) for hibernation and as a page file which can be utilized as extra RAM.
* A home partition which takes up the rest of the free space.

I am dual booting with Windows, and hence have a 182G Windows partition on `nvme0n1p3`.

## Partition Mounting
~~~shell
nick@graviton:~$ cat /etc/fstab
# /dev/nvme0n1p5
UUID=3ae08612-016a-4a5b-8515-36bae186701e       /               ext4            rw,relatime     0 1

# /dev/nvme0n1p1 LABEL=SYSTEM
UUID=4848-C989          /boot/efi       vfat            rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,utf8,errors=remount-ro      0 2

# /dev/nvme0n1p7
UUID=2866f03c-297a-4689-bfe0-c3d9910edcc8       /home           ext4            rw,relatime     0 2

# /dev/nvme0n1p6
UUID=5a3f2d94-3d8a-435a-8699-2024ae5883dd       none            swap            defaults        0 0

/dev/nvme0n1p6: UUID="5a3f2d94-3d8a-435a-8699-2024ae5883dd" TYPE="swap" PARTUUID="c36788f6-48b8-334b-8858-d6a242903682"
~~~

## Boot Manager
As this is a UEFI laptop, I am using [rEFInd](https://wiki.archlinux.org/index.php/REFInd). I had some trouble getting rEFInd to start arch, which was ultimately down to me using an incorrect `PARTUUID` rather than a `UUID` in my `/etc/fstab/`.

My rEFInd config looks like
~~~shell
nick@graviton:~$ cat /boot/refind_linux.conf
"Boot with default options"             "root=/dev/nvme0n1p5 rw add_efi_memmap"
"Boot with fallback  initframs"         "root=/dev/nvme0n1p5 rw add_efi_memmap initrd=/boot/initramfs-linux-fallback.img"
"Boot with default op"                  "root=/dev/nvme0n1p5 rw add_efi_memmap initrd=/initramfs-%v.img"
~~~

## Graphics Card
I chose to use [NVIDIA Optimus](https://wiki.archlinux.org/index.php/NVIDIA_Optimus) for my graphics driver/controller with bumblebee to switch between my GFX card and integrated graphics..Though I am still unsure whether this is working correctly.

~~~shell
nick@graviton:~$ cat /etc/optimus-manager/optimus-manager.conf
[intel]
driver=intel
modeset=yes

[nvidia]
modeset=yes

[optimus]
switching=bbswitch
~~~
with `optirun glxgears` I get ~260fps compared to the usual `60fps`.

## Display Server xorg
Post installation of xorg, `xinit` displayed a black screen with no mouse, due to some problems using the graphics driver.
I am not certain which of the following fixed my issue: installing `bumblebee` and enabling `bumblebee.service`, installing `xf86-video-intel`, or editing the following:

~~~shell
nick@graviton:~$ cat /etc/X11/xorg.conf.d/20-intel.conf
Section "Device"
    Identifier "intelgpu0"
    Driver "intel"
EndSection
~~~

~~~shell
nick@graviton:~$ cat /etc/bumblebee/xorg.conf.nvidia
...
Section "Screen"
    Identifier "Screen0"
    Device "DiscreteNVidia"
EndSection
~~~

## Audio
To get audio working on my non-root user, I had to add my user to the audio group with `sudo usermod -a -G audio nick`.I confirmed [ALSA](https://en.wikipedia.org/wiki/Alsamixer) is working with `speaker-test`. You might need to manually unmute Master in `alsamixer` with the m key.

## Volume & Brighness Keys
As I use function F1-F12 keys often in i3, I generally keep FnLock ON (Fn+Esc).
~~~shell
nick@graviton:~$ cat ~/.xbindkeysrc
#increase volume
"amixer set Master 5%+"
        XF86AudioRaiseVolume

#decrease volume
"amixer set Master 5%-"
        XF86AudioLowerVolume

#mute volume
#"amixer set Master toggle"
"pactl set-sink-mute 0 toggle"
        XF86AudioMute

# Increase brightness
"xbacklight -inc 5"
        XF86MonBrightnessUp

# Decrease Brightness
"xbacklight -dec 5"
        XF86MonBrightnessDown
~~~
Remember to update this file with `xrdb ~/.Xresources`.

## Trackpad Scrolling
In your `~/.bashrc` put
~~~shell
# Reverse scroll direction on trackpad
xinput set-prop "SynPS/2 Synaptics TouchPad" "Synaptics Scrolling Distance" -113 -113
# Touch click
xinput set-prop "SynPS/2 Synaptics TouchPad" "Synaptics Tap Action" 0 0 0 0 1 3 2
# horizontal scroll
xinput set-prop "SynPS/2 Synaptics TouchPad" "Synaptics Two-Finger Scrolling" 1 1
~~~

## Camera
For a non-root user to access the camera you need t add your user to the `video` group with `sudo usermod -a -G video nick`. You can test the camera with `vlc v4l2:// :input-slave=alsa:// :v4l-vdev="/dev/video0"`.
