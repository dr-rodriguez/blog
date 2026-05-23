---
layout: post
read_time: true
show_date: true
title: "Data Science: MongoDB Sky Searches with HEALPix"
date: 2019-06-16
image: /assets/img/posts/2019/20190616/healpixGridRefinement.jpg
tags: [Data Science, NoSQL, Databases, Astronomy, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

This is the third blog post in a series about utilizing MongoDB NoSQL databases with astronomical data. Prior posts introduced how to store [astronomical objects]({% post_url 2019/2019-05-18-data-science-python-dataclasses-and-mongodb %}) and how to store [FITS header metadata]({% post_url 2019/2019-06-01-data-science-astronomy-fits-headers-in-mongodb %}). On today's post, we'll visit one of the most common things we do in astronomy- the cone search. In other words, how to do you search your database for objects in the sky that are located close to your input coordinates. Today we'll be tackling that problem "from scratch" utilizing HEALPix rather than any built-in functionality. As before, I provide a [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/HEALPix_MongoDB.ipynb) in my GitHub repo for those who may want more details and to run it on their own.  
  
**What is HEALPix?**  
HEALPix stands for Hierarchical Equal Area isoLatitude Pixelisation and is an algorithm for dividing up the sky in equal-area pixels. Each pixel can in turn be partitioned to 4 equal-area pixels and so forth providing a progressively finer resolution with each level.  
  
![](/assets/img/posts/2019/20190616/healpixGridRefinement.jpg)  
---  
Diagram of progressively smaller HEALPix pixels. Credit: [NASA JPL](https://healpix.jpl.nasa.gov/)  
The nice thing about HEALPix is that, after you pick a resolution level, each pixel can be represented by a single, unique integer. Well, almost unique- there's the matter of the coordinate grid you are using and the two main ways you can number HEALPix pixels (ring vs nested). For most astronomy applications, though, I'll make use equatorial coordinates on the ICRS reference frame and use a nested numbering scheme. The details of that are not terribly important, but given that other standards (like MOC, mentioned below) use this scheme I recommend sticking to it and avoid having to convert back and forth between reference frames or numbering systems.  
  
There are two main Python packages for working with HEALPix- healpy and astropy-healpix. I found the later easier to use, and the licensing is [more permissive](https://astropy-healpix.readthedocs.io/en/latest/about.html#about). It's fairly straightforward to go back and forth between astronomical coordinates and HEALPix integers and to calculate a set of pixels that are inside a search area:  
  

    
    
    from astropy_healpix import HEALPix, pixel_resolution_to_nside
    from astropy.coordinates import ICRS, SkyCoord
    from astropy import units as u
    
    # nside required for chosen resolution
    resolution = 10 * u.arcsec
    nside = pixel_resolution_to_nside(resolution, round='up')
    
    # HEALPix object with that resolution
    hp = HEALPix(nside=nside, order='nested', frame=ICRS())
    
    # HEALPix to SkyCoord object
    coords = hp.healpix_to_skycoord([42])
    print(coords)
    # Output
    SkyCoord (ICRS): (ra, dec) in deg
        [(44.99038696, 0.00932548)]
    
    # SkyCoord object to HEALPix
    coords = SkyCoord(ra=34*u.deg, dec=-23*u.deg)
    print(hp.skycoord_to_healpix(coords))
    # Output
    9542850888
    
    # Example cone search
    coords = SkyCoord(ra=34*u.deg, dec=-23*u.deg)
    hp_to_search = hp.cone_search_skycoord(coords, radius=5 * u.arcmin)
    print(len(hp_to_search))
    print(hp_to_search[0:10])
    # Output
    6988
    [9542850888 9542850889 9542850891 9542850890 9542850847 9542850845
     9542850839 9542850882 9542850883 9542850886]
    

  
In this example, I used a resolution element of at most 10 arcsecond to initiate my HEALPix object. The smaller the resolution, the larger the integer result for a given coordinate and the more values will be returned for a cone search. This particular case yielded nearly 7,000 healpix values for a cone search of 5 arcminutes. As mentioned earlier, I have a [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/HEALPix_MongoDB.ipynb) that walks you through a bit more detail on this.  
  
**Updating our NoSQL Database**  
An advantage of HEALPix is that it's a single integer that describes a position in the sky and, for a given resolution level, reference frame, and ordering scheme, is unique. As such, you can represent any coordinates you have as a single number (or list of numbers if you are describing an arbitrary shape) and this works very nicely when implementing as an index in any database. Since, we've been working with MongoDB, we'll update our BrownDwarf database we created in the [first tutorial]({% post_url 2019/2019-05-18-data-science-python-dataclasses-and-mongodb %}) to include HEALPix values for all our documents.  
  
Updating everything is actually fairly trivial:  
  

    
    
    import pymongo
    
    client = pymongo.MongoClient()  # default connection (ie, local)
    db = client['test']  # database
    dwarfs = db.dwarfs  # collection
    
    # Loop over those without coords.healpix and set the value
    cursor = dwarfs.find({'coords.healpix': {'$exists': False}})
    for doc in cursor:
        coords = SkyCoord(ra=doc['coords']['ra']*u.deg, dec=doc['coords']['dec']*u.deg)
        healpix = int(hp.skycoord_to_healpix(coords))
        dwarfs.update_one({'_id': doc['_id']}, {'$set': {'coords.healpix': healpix}})
    
    # Create an index on the HEALPix values for faster queries
    if 'healpix' not in dwarfs.index_information():
        dwarfs.create_index([('coords.healpix', pymongo.ASCENDING)],
                              name='healpix', background=True)
    

  
And with that done, we can create a cone search function to easily implement a way to search for objects in our database by their astrophysical coordinates:  
  

    
    
    def cone_search(ra, dec, radius, collection=dwarfs, field='coords.healpix'):
        # Function to perform a simple cone search against a MongoDB collection
        # ra, dec in degrees; radius in arcseconds
        
        coords = SkyCoord(ra=ra*u.deg, dec=dec*u.deg)
        hp_to_search = hp.cone_search_skycoord(coords, radius=radius * u.arcsec)
        cursor = collection.find({'coords.healpix': {'$in': [int(h) for h in hp_to_search]}})
        return cursor
    
    # Example use
    cursor = cone_search(181.9, -39.5, 180.)
    for doc in cursor:
        print(doc['source_id'], doc['coords'])
    # Output
    11 {'ra': 181.889, 'dec': -39.548, 'healpix': 6443584085}
    

  
A fair warning: computing healpix values for a large region of the sky can be a bit time consuming, so you may want to implement cuts in your cone search functions to prevent large queries. Furthermore, you'll need to use the same healpix parameters as otherwise the values will be different and you won't match the correct entries in your database.  
  
**What About MOC?**  
Users familiar with HEALPix may also know about MOC- Multi-Order Coverage maps. These are representations of the spatial coverage of some field of view in a hierarchical format based on HEALPix. As a standard, they use nested numbering in ICRS equatorial coordinates. The key aspect is that if all healpix values at some level are included in the coverage map, MOC would store the lowest level possible. So a representation of MOC would be something like {10: [342, 343], 11: [1145, 1146, 1150]}. I've made up the numbers in this example, but what this means is that at level 10 it includes healpix values of 342 and 343, but at level 11 it has 1145, 1146, and 1150. Each level 10 pixel could be written as 4 level 11 pixels, but by writing it in this fashion it becomes far more compact. You effectively only save the largest possible pixels that encompass your area.   
  
Unfortunately, this does not lend itself very well to the simple cone search functionality we devised above. While python packages exist to represent targets and fields of view as MOC, it's not clear to me that saving a JSON representation of a MOC in a MongoDB database would lead to an efficient search. In my mind, the only way to search for it would be to read out all stored values in the database, reconstruct the MOC objects, and then perform an intersection for your target. If you have millions of sources in your database, I imagine this can become an expensive operation. If you are an expert on MOC or are curious to try this out, let me know. It may be a worthwhile project to investigate and I have a hack day coming up as part of Python in Astronomy 2019. If you'll be attending and interested in NoSQL databases, feel free to touch base with me.  
  
**Final Thoughts**  
In this tutorial, we briefly covered HEALPix to get an integer representation of two-dimension coordinates in space. By doing this, we were able to update our MongoDB database to store these values for our targets and build a simple client-side function to retrieve documents that match a given set of HEALPix values. Because these are just integer numbers, it's trivial to set up in most databases and provides a fast way to implement cone searches. This works for more than just point sources too, since, if your database can store arrays (like MongoDB can), you can store a list of HEALPix values representing the area of your object. This could be used, for example, in our FITS header database to represent the sky coverage of each individual image.  
  
Some databases, including MongoDB, have built-in methods to do spatial indexing. I hope to have a tutorial shortly about MongoDB's geospatial queries to guide you into using their methods to perform similar type of searches as the ones we worked through here. 
