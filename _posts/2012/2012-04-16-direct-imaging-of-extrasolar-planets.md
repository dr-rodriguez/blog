---
layout: post
read_time: true
show_date: true
title: "Direct Imaging of Extrasolar Planets"
date: 2012-04-16
image: http://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Beta_Pictoris_system_annotated.jpg/603px-Beta_Pictoris_system_annotated.jpg
tags: [Astronomy, Planets]
category: Astronomy
author: Strakul
description: ""
img_show: false
---

Right now there are hundreds of planets known in the Galaxy and thousands of candidates identified with the Kepler space telescope. Most of these planets are inferred through indirect means, the most popular ones being the radial velocity method, where the planet's gravity pulls on the star and we see this signature, or the transit method, where the planet passes between us and the star causing a tiny decrease in the star's brightness. Perhaps at some point I'll talk about these methods, but today I wanted to briefly mention one of the more exciting methods to search for planets: direct imaging.  
  
What is Direct Imaging?  
This is exactly what it sounds like. It involves obtaining a deep image around a nearby star and actually spotting planets in orbit around that star. Having an actual image is (especially to the public) one of the more convincing lines of evidence for the existence of these extrasolar planets. Naturally, there are complications.  
  
![](http://upload.wikimedia.org/wikipedia/commons/thumb/a/ae/Beta_Pictoris_system_annotated.jpg/603px-Beta_Pictoris_system_annotated.jpg)  
---  
_A composite image of beta Pictoris. The outer parts reveal the dust disk in scattered light, the inner part reveals a planet in infrared light. The star has been removed to reveal these faint features. Credit: ESO/A.-M. Lagrange et al. ([link](http://www.eso.org/public/news/eso0842/))_  
Consider two objects separated by the same distance, say two friends standing a few feet apart. If instead of measuring the physical distance you measure the angle between these two people, you will notice something interesting. The angle between these two gets smaller as you move farther away, but larger as you move towards them. If you know how far you are from your friends and have measured the angle between them, you can work out what the physical separation is between your two friends.  
This angular separation is what we generally deal with in astronomy. Furthermore, because stars are so far away, these separations are very small. Even though planets may be tens to hundreds of astronomical units (AU) away from the star [(remember AU](http://strakul.blogspot.com/2012/02/distances-in-astronomy.html)? this is the separation between the Earth and the Sun, about 150 million kilometers), the angular separation may be only up to a few arcseconds. What's an arcsecond? It's a unit to measure angle which corresponds to 1/60 of an arcminute, which itself is 1/60 of a degree. For comparison, the full moon is about half a degree across (or 30 arcminutes) and the International Space Station can appear up to one arcminute in size, depending on the orientation. Hence, any exoplanet that we attempt to image will appear extremely close to the host star it orbits.  
  
This poses another problem: stars are brighter than planets. Planets will only shine by reflecting light or emitting light by virtue of their temperature. In either case this is much less than that of the star. The Earth's atmosphere will tend to blur out the light from stars making detection of planets impossible. The planet's feeble light would be completely washed out by the light of the star. We thus need to do two things. One is to account for the atmosphere, either by performing observations in space (where we don't have to worry about the atmosphere), or by correcting for the atmosphere's effects, which I'll describe below. The other is to block out or subtract the light from the star so what we can see fainter objects around it.  
  
Adaptive Optics  
Adaptive optics, or AO, is a technique used to measure and correct distortion in astronomical images as a result of the turbulence in Earth's atmosphere. The basic idea is that we measure the distortion caused by the atmosphere and use a deformable mirror to correct for it. In order to measure the distortion we use a bright star and observe how the atmosphere affects it. For planet searches this is usually the target star, as it is generally bright. However, for more distant stars, or lower-mass objects, or even distant galaxies, you can't rely on the object itself. You then pick a nearby bright source, but then you run into some problems. First is that you may not have a bright source nearby. Second is that even if you do, you are in principle correcting a slightly different patch of the atmosphere. Hence, the image for the object of interest may still not be great. A workaround this is to use a laser guide star. This involves shinning a very powerful laser up to the Earth's upper atmosphere. This excites sodium particles which causes a bright, artificial star to show up in the field of view. With this artificial star you can measure most of the distortion from the atmosphere and thus perform your corrections.  
  
![](http://www.astro.caltech.edu/palomar/images/aooffon.jpg)  
---  
_Binary star IW Tau without and with adaptive optic corrections.  
Credit: Chas Beichman and Angelle Tanner of JPL_  
The end result for AO imaging is a much sharper image as the atmosphere would naturally tend to blur things out. This is important as it allows us to probe very close to the stars. As previously mentioned, any planets would appear to be very close to their stars so you need something like adaptive optics to spot them. AO is already widely used in many large telescopes, such as the Keck, Gemini, and VLT telescopes. Even 'smaller', 3-m class telescopes sometimes have them, like at Lick Observatory.  
  
![](/assets/img/posts/2012/20120416/laservlt_eso.jpg)  
---  
_Using the laser guide star at the Very Large Telescope (VLT) in Chile. Credit: Yuri Beletsky (ESO)_  
  
Removing the Star  
There are two main ways to remove the light from the star. The first is to implement a physical mechanism to block out the star. This is known as a coronograph and is a small obstruction (a spot) that prevents the light from that location from reaching the detector. The star is placed right behind it and most of its light is blocked out. Some facilities have multiple spot sizes which allow you to block the light of the star out to certain distances, like 20 AU or perhaps 60 AU. The more light you block the easier it is to find the planets, but if you use a large spot you may also block the planet itself.  
  
Another method is to remove the light digitally after taking the images. This can be very tricky as even AO corrected images can vary from one exposure to the next. One can take an image of a comparison star and subtract this, but even that is somewhat limited. One clever technique, known as angular differential imaging, is to take many images while allowing the field of view to rotate. If one then takes the average of all these images, or frames, you would get a nice reference star image you can use to subtract. Because the field has rotated the planet wouldn't be part of the reference image. You can subtract the reference star image from each frame and then rotate the subtracted images. Doing the rotation will cause the planet to be lined up in all frames and you can combine them all to produce a deep image of the field, without most of the light of the star, that will reveal any planets in the system.  
  
![](http://upload.wikimedia.org/wikipedia/commons/c/c2/Benjamin_Zuckerman_HR_8799_planets_image_Dec._2010.jpg)  
---  
_The HR8799 planetary system. The star has been digitally subtracted to reveal the four planets.  
Credit: Marois et al. (2010 Nature)_  
  
The Future in Direct Imaging  
While extrasolar planets will still be found via the radial velocity or transit methods, more and more work is being done to push the boundaries of direct imaging. Several instruments are under construction, such as [GPI](http://planetimager.org/) and [SPHERE](http://www.mpia.de/SPHERE/index.php), and will soon be placed on large telescopes. These will be much more effective at correcting for the atmosphere's turbulence and allow us to probe deeper and closer to the star. These instruments will likely focus at first on the nearest, brightest stars. One important consideration, though, is the age of the system. What we observe in directly imaged planets is the heat they give off as they cool. Older systems would have planets that have cooled off much more, and the colder the planet, the fainter it is. Hence, the youngest stars will be prime targets for future AO surveys. Unfortunately, most young stars are many hundreds of light-years away in distant star forming regions. However, recent work in the past two decades has revealed groups of stars younger than 100 million years (that's young, for stars) in close proximity to the Earth: all within about 300 light-years. These will be prime targets for AO studies. My own research has focused on finding additional low-mass members to these groups and I'll be excited to see what AO surveys turn up in these newly identified stars. Many of these nearby, young stars are in the Southern Hemisphere so Chile will greatly contribute to these planet searches (particularly since GPI will be on Gemini South and SPHERE will be on VLT, both located in Chile). 
