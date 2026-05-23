---
layout: post
read_time: true
show_date: true
title: "Data Science: MongoDB Sky Searches with Geospatial Queries"
date: 2019-07-07
image: /assets/img/posts/2019/20190707/768px-WGS84_mean_Earth_radius_1.png
tags: [Data Science, Databases, NoSQL, Astronomy, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

This is the fourth and, for now, final set of posts in my tutorial on using MongoDB No-SQL databases for astronomical work. We've created a [database of Brown Dwarf objects](http://strakul.blogspot.com/2019/05/data-science-python-dataclasses-and.html) making use of Python 3.7's Dataclasses, we've also [stored header metadata](http://strakul.blogspot.com/2019/06/data-science-astronomy-fits-headers-in.html) for a variety of FITS files, and we've written functions to perform [cone searches using HEALPix](http://strakul.blogspot.com/2019/06/data-science-mongodb-sky-searches-with.html). Today, we're looking again at how to query the sky, but this time using MongoDB's built-in geospatial's functionality. As before, I provide a [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/Spatial_MongoDB.ipynb) where those interested can follow along.  
  
**GeoJSON**  
MongoDB uses [GeoJSON objects](https://docs.mongodb.com/manual/reference/geojson/index.html) to store geometry information in its database. It's actually fairly straightforward to create an object of this type. Here's how a few of them look like:  
  

    
    
    {'type': 'Point', 'coordinates': [42.0, -23.0]}
    {'type': 'Polygon', 
     'coordinates': [[[0.0, 0.0], [3.0, 6.0], 
                      [6.0, 1.0], [0.0, 0.0]]]}
    

  
There's a variety of shape options to use from, including polygons, multipolygons, points, but unfortunately not circles (though you can approximate a circle as an N-sided polygon).  
  
If your database contains some geoJSON objects, you can create a [2dsphere index](https://docs.mongodb.com/manual/core/2dsphere/), to make use of a variety of operators to search your database. These include $geoWithin, which returns objects that are entirely within a geoJSON geometry you specify; $geoIntersects, which returns anything that intersects your specified geometry; $near and $nearSphere, which returns an ordered list of targets within some $maxDistance in **meters**. This last part should illustrate what problem we'll run into: these queries are based on the assumption that you are querying on the Earth's surface. We'll need to make some changes to use them for astronomical sources.  
  
**World Geodetic System**  
MongoDB, and in fact a lot of other applications too, make use of the World Geodetic System as a reference frame for any spatial information. The version used by MongoDB is [WGS84](https://spatialreference.org/ref/epsg/4326/), which is also used by things like the Global Positioning System. Now, I'm not an expert at geodesy, but as I understand it this provides a model of an oblate spheroid for the Earth whose coordinates are based on the Earth's center of mass. Because the Equatorial system of astronomical coordinates is based on the projection of Earth's longitude and latitude lines out into space, we can express Right Ascension and Declination as longitude and latitude, respectively. The only concern we would need to worry about is that in astronomy we deal with a perfect unit-sphere whereas on Earth we need to take into account the oblateness of the Earth.  
  
[![](assets/img/posts/2019/20190707/768px-WGS84_mean_Earth_radius_1.png)](assets/img/posts/2019/20190707/768px-WGS84_mean_Earth_radius.png)  
---  
The 1984 World Geodetic System revision, not to scale  
  
**Modifying for Astronomy**  
In order to use MongoDB queries for astronomical sky searches we need to take into account a few things. First, longitude coordinates must be bound between -180 and 180. Second, most operators require distances in meters, rather than degrees or arcseconds. And third, how many meters there are in a degree will vary by latitude. These considerations are enough to get us to what we need.  
  
To calculate the scaling between degrees and meters, we can make use of the below function, which computes the radius of curvature at the Meridian and returns how much 1 degree is in meters. The basic equation for the radius of curvature is as follows, where a is the semimajor axis, e the eccentricity, and phi the latitude:  


[![](assets/img/posts/2019/20190707/curvature_1.png)](assets/img/posts/2019/20190707/curvature.png)

  
And here is that expressed as a Python function:  

    
    
    import numpy as np
    
    def wgs_scale(lat):
        # Get scaling to convert degrees to meters for a geodetic latitude
        
        # Values from WGS 84
        a = 6378137.000000000000 # Semi-major axis of Earth
        b = 6356752.314140000000 # Semi-minor axis of Earth
        e = 0.081819190842600 # eccentricity
        angle = np.radians(1.0)
        
        # Compute radius of curvature along meridian 
        # (see https://en.wikipedia.org/wiki/Meridian_arc)
        rm = a * (1 - np.power(e, 2)) / np.power( ( (1 - np.power(e, 2) * 
             np.power( np.sin(np.radians(lat)), 2) ) ), 1.5)
        
        # Compute length of arc at this latitude (meters/degree)
        arc = rm * angle
        return arc
    

  
In practice, one would need to integrate along the path to get the actual distance to use so this will never be perfect. However, it's good enough for a first pass and certainly good if your queries are moderately small.  
  
**Cone Search Example**  
Let's assume you have a database set up with a key 'coords.loc' that contains a geoJSON (you can refer to the notebook for how to create that from the BrownDwarf database we built earlier). There's many ways to query, but I'll present two ways.  
  
First, we'll make use of the $nearSphere operator to write a simple cone search:  

    
    
    def cone_search(ra, dec, radius, collection=dwarfs, field='coords.loc'):
        
        scaling = wgs_scale(dec)
        meter_radius = radius * scaling
        lon, lat = ra-180., dec
        
        cursor = collection.find({'coords.loc': {
        '$nearSphere': { 
            '$geometry': { 'type': 'Point', 
                          'coordinates': [lon, lat]}, 
            '$maxDistance': meter_radius
        } } })
        
        return cursor
    
    # Use the function
    cursor = cone_search(220., 12., 10.)
    for doc in cursor:
        print(doc['source_id'], doc['coords']['ra'], 
              doc['coords']['dec'], doc['coords']['loc'])
    
    # Output
    4 222.106791 10.533056 {'type': 'Point', 'coordinates': [42.10679099999999, 10.533056]}
    7 219.868167 19.487472 {'type': 'Point', 'coordinates': [39.868167, 19.487472]}
    

  
Next, we'll use the $geoNear operator to also include queries in addition to a calculated distance column:  

    
    
    def cone_search_advanced(ra, dec, radius, query={}, 
        collection=dwarfs, field='coords.loc', distance_field='distance'):
        
        scaling = wgs_scale(dec)
        meter_radius = radius * scaling
        lon, lat = ra-180., dec
        
        cursor = dwarfs.aggregate( [
       {
          '$geoNear': {
             'near': { 'type': "Point", 'coordinates': [ lon, lat ] },
             'spherical': True,
             'maxDistance': meter_radius,
             'query': query,
             'distanceField': distance_field
          }
       } ] )
        
        return cursor
    
    # All M-dwarfs within 45 degrees of 220, -32, sorted by distance
    cursor = cone_search_advanced(220., -23., radius=45, 
                                  query={'spectral_type' : {'$regex': 'M'}})
    for doc in cursor:
        print(doc['source_id'], doc['spectral_type'], 
              doc['coords']['ra'], doc['coords']['dec'], 
              round(doc['distance']/wgs_scale(doc['coords']['dec']), 2))
    
    # Output
    83 M9.0 236.94662 -24.397028 15.65
    61 M9.0 191.309 -44.485477 31.86
    11 M8.0 181.889 -39.548 36.27
    

  
For more details, I'll refer you to the [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/Spatial_MongoDB.ipynb) where I also compare these on-sky-distance estimates to real-sky calculations with astropy. In short, they are pretty accurate if your separations are within a few degrees. This is to be expected since we're converting meters to degrees and back for just a single location. You can always consider doing a larger search and then trimming it by calculating the real-sky distances to get more accurate results.  
  
**Final Thoughts**  
Using MongoDB's built-in spatial functionality is a lot faster than trying to code something manually with HEALPix or similar. However, you have to remember to implement some important work-arounds to make full use of MongoDB's geospatial indexing and query capabilities since those were built for searches along the Earth's surface. Even then, you may also need to manually tweak results, by say calculating real-sky separations, to get the best results, though that can be done on a final clean up stage after you have some results in your client software.  
  
Overall, this has been a great way for me to explore MongoDB's potential for astronomy. I'm sure I've missed a lot of useful tricks and features and there are probably use-cases I didn't even consider. If you decide to implement this in your own research or applications, or if you have ideas for additional topics I could explore here, feel free to drop me a line by commenting below or on my twitter feed. 
