---
layout: post
read_time: true
show_date: true
title: "How to Make 3D Images with GIMP"
date: 2012-03-03
image: /assets/img/posts/2012/20120303/jt07_3d_1.png
tags: [Misc, Pictures]
category: Misc
author: Strakul
description: ""
img_show: false
---

Recent years have seen an increase in 3D movies wherein you use special glasses to see a film with an added perception of depth. It turns out you can easily do the same with a basic camera, free software, and some careful planning. When using red/blue glasses (or any two opposite colors, really) these are known as [anaglyphs](http://en.wikipedia.org/wiki/Anaglyph_image). I've been creating these since 2009 and have uploaded some to my [Picasa albums](https://picasaweb.google.com/107352926946754821853). In this post, I will describe how I generate these.  


  


**Step 1: Take the Pictures**

You need at least 2 pictures for this to work. The important thing to realize is that human eyes are separated by a few centimeters. If you want to duplicate this effect, you need to take two photos and move between each one. If you want a more pronounced effect, you can shift more than a few centimeters, but I wouldn't recommend shifting by more than a foot (30 cm). 

An alternative is to have two cameras attached together and going off at the same time. The advantage there is that by taking simultaneous images you don't have to worry about any moving objects, rotating the field of view, or capturing a slightly different angle. Unfortunately, you do need two cameras and a more complicated setup.

  


Let's consider you have one camera only, like I do. You take image 1, then you shift by about 10-20 cm or so to the right and, facing the same direction, take image 2. Ideally you will not have any moving objects (cars, birds, etc) in your frames. If you do, these will ruin the desired effect. Try not to rotate the camera in any way when shifting, just face the same direction. The two images will look very similar, but there will be minor perspective changes. It is these changes that will provide the illusion of depth.

  


Here are two such frames I've taken. I've placed them side by side for two reasons. One is so you can compare them. The other is that by displaying these images side by side and crossing your eyes you can see a 3D image and can check that everything is fine. It takes a bit of practice to learn how much to cross your eyes. You basically want to see 3 images, the one at the center will be the combination of both of them and will have the added depth perspective. Remember that you can click these images to see them larger.

[![](assets/img/posts/2012/20120303/jt07_3d_1.png)](assets/img/posts/2012/20120303/jt07_3d.png)

  


The images above were taken with a SONY DSC-S500 6MP camera so as to demonstrate that you don't really need a fancy camera for this. I've used the iPhone camera to make good anaglyphs as well. I usually (like in this case) reduce the raw image size to 1024x768 so as to make them easier to process.

  


**Step 2: Load up the images on GIMP**

[GIMP](http://www.gimp.org/) is a free and powerful image processing tool. The basic tasks to produce an anaglyph are fairly simple, so any decent image processing program should be able to work. So feel free to use Photoshop or similar programs if you have them.

  


You can either open up the image directly in GIMP or open the program blank and copy-paste the image onto the frame. Dealing with large images can take up a lot of processing power. It's up to you how big you want your images, but as this technique does have it's faults I try not to go beyond 1024 pixels in one dimension. If you have a good camera, a steady hand, and a beautiful shot, then go ahead and use the largest you can handle!

  


You want to load up the two images in separate layers. In GIMP, this is accomplished easily. When you paste the frame, click on the new layer icon on the Layers Dialog window and it will automatically create a new layer with the image you just pasted. Or even easier: just go to Edit -> Paste As -> New Layer.

  


**Step 3: Make Some Color Layers**

Set a new foreground color to FF0000; this is red. Make a new layer and select the Fill Type to be the foreground color. You will have a uniform red layer. Set the layer mode to Screen, duplicate this layer, then do Color -> Invert. That will basically produce another layer with color 0000FF, which is cyan. You could have created the second layer the same way as the first layer, though. The important thing is that both layers must have mode=Screen activated (you can do set on the Layers Dialog window).

  


**Step 4: Merging Layers**

Move the layers so that you have them in this order: Cyan, Image 2, Red, Image 1. Image 1 is the left-hand image I have above and Image 2 is the right-hand image. It is very important to get this right otherwise you will need to flip your red/blue glasses backwards (your left eye should see the red image). Activate only the Cyan and Image 2 layers (ie, hide the other two). I do this by clicking the eye symbols on the Layers Dialog window. Merge those two layers (Image -> Merge Visible Layers) and set the mode to Multiply. Now activate all layers and look at the result:

[![](assets/img/posts/2012/20120303/raw1.png)](assets/img/posts/2012/20120303/raw1_1.png)

  


That works, but I personally don't like it. The shift is a little bit too large, especially for the center feature. This brings us to the last step.

  


**Step 5: Tweak to Perfect**

With the cyan-merged frame selected, use the Move tool to tweak the image until getting a desirable result. You may also find that you need to apply a small rotation. I personally like distant objects to be neutral (ie, no red/cyan on their edges). However, that may cause some of the foreground material to be too far shifted. You want to hit a balance such that things don't appear too close or your eyes will have trouble seeing details. Here I aim for such a balance and crop out some of the edges:

[![](assets/img/posts/2012/20120303/jt07_1.png)](assets/img/posts/2012/20120303/jt07.png)

  
This is the version I'm happy with and so I save it as a PNG or any other format I prefer.

  


Images taken July 2011 at Joshua Tree National Park in California. More such anaglyphs I've created can be found in my [Picasa albums](https://picasaweb.google.com/107352926946754821853).  
  
**Remember** that there is some eye strain when crossing your eyes and when looking at these artificial 3D images, so don't spend too much time looking/creating them or you'll end up with a headache.  
  


Common pitfalls to avoid:

  * The two images are slightly rotated. This can be fixed in GIMP, but is a bit more involved.
  * Different light levels in the images. Again this can be fixed, but ideally you want similar light levels on both raw images.
  * Intense red/blue colors. Most red/cyan glasses aren't perfect and if you have some bright colors this can mess up the intended effect. For example, a red rose will not end up very nice as an anaglyph.
  * Moving objects. Sometimes a bird in the background appears in only one frame, or the wind shifted a flag, or you have a shot of the sea. If it has motion, it's going to be tricky to arrange with this simple setup. You may have to use some dual-camera setup.



* * *

  
A few anaglyphs I've made are displayed below for your viewing pleasure, more can be found [here](https://picasaweb.google.com/107352926946754821853):  
[![](assets/img/posts/2012/20120303/kpno01_1.png)](assets/img/posts/2012/20120303/kpno01.png)  
---  
The Kitt Peak National Observatory (KPNO)  
[![](assets/img/posts/2012/20120303/3meter_an_1.jpg)](assets/img/posts/2012/20120303/3meter_an.jpg)  
---  
The Shane 3-m telescope at Lick Observatory  
[![](assets/img/posts/2012/20120303/mnh01.png)](assets/img/posts/2012/20120303/mnh01_1.png)  
---  
Camptosaurus at the Museum of Natural History in Los Angeles  
[![](assets/img/posts/2012/20120303/ctio14.png)](assets/img/posts/2012/20120303/ctio14_1.png)  
---  
The Cerro Tololo International Observatory (CTIO) in Chile  
[![](assets/img/posts/2012/20120303/pandaSD_1.png)](assets/img/posts/2012/20120303/pandaSD.png)  
---  
A panda at the San Diego Zoo 
