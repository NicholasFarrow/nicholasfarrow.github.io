---
layout: posts
title: "Printing a Star Wars Scene in Terminal with Bash [towel.blinkenlights.nl]"
author_profile: true
last_modified_at: 2019-06-07T16:20:02-05:00
date: 2019-06-07
header:
  teaser: "/assets/images/starshell.png"
  og_image: "/assets/images/starshell.png"
layout: single
classes: wide
author_profile: true
read_time: true
comments: # true
share: true
related: true
---
You may remember being able to watch a 17 minute [ASCII animation of Star Wars IV](https://www.asciimation.co.nz/) in terminal, from ~15 years ago (I remember watching it on my primary school's library computers).

(You can download my MOTD bash script [here](https://github.com/nickfarrow/plugfiles/blob/master/starshell.sh))

Well over a decade later we can still watch it using telnet. By typing into our terminal:
```shell
$ telnet towel.blinkenlights.nl
```

![StarWars Example](/assets/images/starshell4.png)

We would like to write a bash script which on the first execution saves each frame of the animation to a file, and upon subsequent excecutions, print a random frame to the terminal.

First we need to download each frame of the ASCII Star Wars animation. Each frame is separated by the character sequence `^[[H`.

We pipe the output of telnet into a command `sed` which allows us to perform a regular expression command on the frames. We use  regex 's/.\[H/Z/g' to replace every occurence of `^[[H` with `Z`.
```shell
$ telnet towel.blinkenlights.nl | sed -e 's/.\[H/Z/g'
```
(`Z` conveniently does not appear elsewhere in the animation)
Next we want to use `tee` to output this to a file  `mainScenes`:
```shell
$ telnet towel.blinkenlights.nl | sed -e 's/.\[H/Z/g' | tee mainScenes
```

We sit through the whole animation as it saves each frame to the file.
```shell
$ cat mainScenes
.
.
             /    /  \   \                             ===,              
            |    |><> |   |         Come on!          @o o@)             
            |    (/\__)   |                            \- /              
            |   /      \  |                          /       \            
            |   ||/(===o  |                         / (   )  \           
            |   | /  \ |  |                        /_/\    /\_|          
            |   \/][][\/  |                        ||  \  // ||          
            |    |\  /|   |                         @  | | | @           
            |    |_||_|   |                            | | |             
            |    [ ][ ]   |                            | | |             
            |    | || |   |                           /  |  \            
            |    | || |   |                           ``````~            
      ______|___/__][_]___|___________________________/__)(_)____________Z

             /    /  \   \                             ===,              
            |    |><> |   |                           @o o@)             
            |    (/\__)   |                            \- /              
            |   /      \  |                          /~~  ~~\            
            |   ||/(===o  |                         / (   )  \           
            |   | /  \ |  |                        /_/\    /\_|          
            |   \/][][\/  |                        ||  \  // ||          
            |    |\  /|   |                         @  | | | @           
            |    |_||_|   |                            | | |             
            |    [ ][ ]   |                            | | |             
            |    | || |   |                           /  |  \            
            |    | || |   |                           ``````~            
      ______|___/__][_]___|___________________________/__)(_)____________Z
```
We can see each frame is now separated by character `Z`.

Now we wish to load this file `mainScenes` and save each frame to an individual file. We open the file into a variable `$SCENEDATA` :
```shell
SCENEDATA=$(<"mainScenes")
```
And then we split this string, on each occurence of `Z`, into an array `$SCENES` using an [*Internal Field Separator (IFS)*](https://www.cyberciti.biz/faq/unix-howto-read-line-by-line-from-file/):
```shell
IFS=$'Z' read -d '' -ra SCENES <<< "$SCENEDATA"
```
Next we loop over an index for each scene, through numbers 115- with `seq 115 ${#SCENES[@]}`.  We start from scene #115 to skip the classic Star Wars scrolling text intro. During the loop we echo each scene into a file `starshell/s_INDEX`. You should `mkdir starshell` yourself:
```shell
$SCENEDIR=starshell

# Save "good" scenes to files
for SCENENUM in $(seq 115 ${#SCENES[@]}); do
    echo "${SCENES[$SCENENUM]}" >> $SCENEDIR/s_$SCENENUM    
done
```
We can see all the scenes:
```shell
$ ls -l starshell/
```
```shell
-rw-r--r-- 1 nick nick 968 Jun  6 04:54 s_1000
-rw-r--r-- 1 nick nick 968 Jun  6 04:54 s_1001
-rw-r--r-- 1 nick nick 968 Jun  6 04:54 s_1002
...
```

Now all that is left is to randomly choose a scene to print to the terminal. We choose randomly by listing the `$SCENEDIR` directory, and then pipe each filename into `shuf`; which randomly sorts them, then chooses the first one with flag `-n 1`:

```shell
$ echo "$(<$(ls "$SCENEDIR/s_"* | shuf -n 1))"
```

Upon running this a random scene is displayed!
```shell
            /~\                                   |       _______        
           ( oo|                                  | ""   /       \  ooo*
           _\=/_                                  | :.  |__[]_[]__| o*o*
          /     \                       ___       |     |__  _  __| *oo*
         //|/.\|\\                     / ()\      | ##  |  [] []  | o*oo
        ||  \_/  ||                  _|_____|_    | ##   \_______/       
        || |\ /| ||                 | | === | |   |______________________
        #  \_ _/ #                  |_|  O  |_|  /                       
           | | |                     ||  O  ||  |------------------------
           | | |                     ||__*__||  | (*)                    
           []|[]                    |~ \___/ ~| |                        
           | | |                    /=\ /=\ /=\ |                        
      ____/_]_[_\___________________[_]_[_]_[_]_|________________________
```
Let's wrap this all up into a single file:
```shell
#!/bin/bash
# Nicholas Farrow 2019
CFGLOC=~/.config
SCENEDIR=$CFGLOC/starshell

STARTSCENE=115

# Make folder if doesn't exist
[ -d $SCENEDIR ] || mkdir $SCENEDIR

# If $STARTSCENE scene file does not exist, save starwars to file
if [ ! -f $SCENEDIR/s_$STARTSCENE ]; then
    telnet towel.blinkenlights.nl | sed -e 's/.\[H/Z/g' | tee $SCENEDIR/main

    SCENEDATA=$(<"$SCENEDIR/main")

    # Load scenes
    IFS=$'Z' read -d '' -ra SCENES <<< "$SCENEDATA"

    echo ${#SCENES[@]}

    # Save "good" scenes to invividual files
    for SCENENUM in $(seq 115 ${#SCENES[@]}); do
         echo "${SCENES[$SCENENUM]}" >> $SCENEDIR/s_$SCENENUM
         echo $SCENENUM
    done
fi

# Print random scene
echo "$(<$(ls "$SCENEDIR/s_"* | shuf -n 1))"
```

Save this script in your home directory like `~/.config/starshell.sh`, and make it excecutable with `chmod +x ~/.config/starshell.sh`. If you want a scene automatically displayed each time you open a terminal then in your `~/.bashrc` or  `~/.bash_profile` we add the line:
```shell
source  ~/.config/starshell.sh
```
Now every time we open a new terminal a nice scene is displayed! (majority of scenes are quite nice).

I would love for someone to try this on Mac OSX, let me know if it works out!

![Terminal Examples](/assets/images/starshell2.png)

Or alternatively we can manually print out a new scene by typing:
```shell
~/.config/starshell.sh
```
