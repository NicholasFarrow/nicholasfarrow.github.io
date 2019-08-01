---
layout: posts
title: "Creating a Moon Animation Using NASA Images and Python"
author_profile: true
last_modified_at: 2019-08-02T03:20:02-05:00
date: 2019-08-02
header:
  teaser: "/assets/images/post3/header.png"
  og_image: "/assets/images/post3/header.png"
layout: single
author_profile: true
read_time: true
comments: # true
share: true
related: true
---
We can create this amazing HD animation with just a few lines of python code!
![moon gif](/assets/images/post3/animation.gif)

We can check what the moon will look like at a given day and time using [this NASA visualisation](https://svs.gsfc.nasa.gov/4442).
![screenshot1](/assets/images/post3/screenshot_1.png)

By opening the image in a new tab, we see the image is stored with the path:
`https://svs.gsfc.nasa.gov/vis/a000000/a004400/a004442/frames/730x730_1x1_30p/moon.5108.jpg`

Replacing the last number in the URL (5108) with another number, 0001, we can view the first image:
![0001](https://svs.gsfc.nasa.gov/vis/a000000/a004400/a004442/frames/730x730_1x1_30p/moon.0001.jpg)

We want to write some python which downloads and saves one of these images. We can store the URL as a string:
~~~python
imageNumber = "0001"
URL = "https://svs.gsfc.nasa.gov/vis/a000000/a004400/a004442/frames/730x730_1x1_30p/moon.{}.jpg".format(imageNumber)
~~~

We can use the `urllib` module to download this image to a folder we have created `out`.
~~~python
import urllib.request as request
request.urlretrieve(URL, 'out/image0001.jpg')
~~~

We can now generalise this by creating a python function which takes an input `imageNumber` and output`directory`:
~~~python
def getImage(imageNumber, directory):
    URL = "https://svs.gsfc.nasa.gov/vis/a000000/a004400/a004442/frames/730x730_1x1_30p/moon.{}.jpg".format(imageNumber) 
    saveName = directory + imageNumber + ".jpg"
    request.urlretrieve(URL, saveName)
    return
~~~
Now we can easily download image #0999 into folder `out` with `getImage("0999", 'out/')`.

We wish to download all 8761 images to use in creating an animation. Before the downloading, first we write a small function which assists with the '0001' formatting style (moon.1.jpg does not exist!). 
We want a function which takes an integer input `n`, and then adds some leading `0s`. The number of zeros required is equal to 4 minus the length of the number as a string i.e `4 - len(str(n))`.

~~~python
def addZeros(imageNumber):
    numAdd = 4 - len(str(imageNumber))
    return "0" * numAdd + str(imageNumber) 
~~~

Now we can download image #3 using getImage(addZeros(3), 'out/').

From here we can download all 8761 images:
~~~python
for imageNumber in range(1, 8761):
    print("Downloading image #{}".format(imageNumber))
    getImage(addZeros(imageNumber), 'out/')
~~~
These images might take a while to download!

Once all the images have been downloaded we can create a video using the `imageio` module. First we create a list containing all the file locations. We can do this in a traditional loop:
~~~python
files = []
for imageNumber in range(1, 8761):
    files.append('out/' + addZeros(imageNumber) + '.jpg')
~~~
or we can do this in a more pythonic way using list comprehension:
~~~python
files = ['out/{}.jpg'.format(addZeros(imageNumber)) 
        for imageNumber in range(1, 8761)]
~~~

Now we can load all these images with `imageio` in a similar way:
~~~python
import imageio
images = [imageio.imread(file) for file in files]
~~~

Finally, we can create a gif using:
~~~python
imageio.mimsave('moonAnimation.gif', images)
~~~

![moon gif](/assets/images/post3/animation.gif)

[An old implimentation of the full code can be found on my Github.](https://github.com/NicholasFarrow/moonPhase/)

