---
layout: post
read_time: true
show_date: true
title: "Data Science: Astronomy FITS Headers in MongoDB"
date: 2019-06-01
tags: [Data Science, NoSQL, Databases, Astronomy, Python]
category: Data Science
author: Strakul
description: ""
---

This is the second post I have about using [MongoDB](https://www.mongodb.com/) NoSQL databases with astronomical data. If you'd like a refresher about what that means, check out [my first post](https://strakul.blogspot.com/2019/05/data-science-python-dataclasses-and.html), where I describe how to ingest a custom BrownDwarf class object into these type of databases. Today, we're looking at a more general problem- metadata. Metadata is the information that describes the how, when, where of the data itself. For example, which telescope took the data, at what time of night, for how long, with what filter, etc etc. A lot of this information is encapsulated in the data files itself and, currently, the most commonly used format in astronomy is the FITS file.  
  
In this post, we'll have a look at how we can extract the metadata from a FITS file and load it into our NoSQL database. As before, I provide a [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/FitsHeaders_MongoDB.ipynb) if you'd like to run the code yourself.  
  
**The FITS File**  
FITS stands for Flexible Image Transport System and is a file format standard since 1981. It's grown and been iterated quite a bit and replacements are frequently talked about, so perhaps in a few years we'll have another format to work with. It's not just for imaging data either, FITS can store tables, data cubes, and much more. One of the core aspects of the FITS file is the header, which contains metadata about the contents. It consists of multiple 'cards', which are key-value pairs storing some information the creator of the FITS file wished to save. From our discussion last time, you may remember that key-value pairs are the bread and butter of NoSQL databases so this should be a natural place to store this information as well.  
  
**Using Astropy for Metadata**  
There are many ways to get at the FITS header, particularly since it's stored as human-readable ASCII. For our example, we'll be using the [astropy package](https://www.astropy.org/) as it's built to deal with astronomical data in general. What we're interested in is just the header; we want to see how we can extract this information and how we can convert it to a JSON format to store as a document in a MongoDB database.  
  
For the full details, I'll refer you to my [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/FitsHeaders_MongoDB.ipynb) or the astropy [documentation](https://docs.astropy.org/en/stable/io/fits/index.html), but here's what I ended up using. It's a simple class that reads in the file, extracts the metadata, and converts it to a dictionary. I provided methods to convert the data to a JSON file as well as a method to load it to a specified mongoDB collection. Without too much difficulty, I could write a script to crawl through directories, look up FITS files, and populate them to my database utilizing this wrapper code.  
  

    
    
    import json
    import os
    import json
    from astropy.io import fits
    
    
    class FitsWrapper:
        def __init__(self, fits_filename):
            hdul = fits.open(fits_filename)
            self.h = hdul[0].header
            hdul.close()
            
            self.data = {}
            for i, c in enumerate(self.h.__dict__['_cards']):
                if c[0] == '': continue
                    
                # Append to existing cards
                if self.data.get(c[0], None) is None:
                    self.data[c[0]] = c[1]
                else:
                    self.data[c[0]] = self.data[c[0]] + ' ' + c[1]
                
            if self.data.get('FILENAME', None) is None:
                self.data['FILENAME'] = os.path.basename(fits_filename)
    
        def to_json(self):
            return json.dumps(self.data, default=lambda x: x.__dict__, indent=4)
            
        def load_mongodb(self, collection, verbose=False):
            json_data = json.loads(self.to_json())
            
            # Replace any existing document that matches the filter. 
            # If none is matched, upsert=True creates a new document.
            result = collection.replace_one(filter={'FILENAME': self.data['FILENAME']}, 
                                            replacement=json_data, upsert=True)
            
            if verbose:
                print('Modified: ', result.modified_count)
                print('Insert ID: ', result.upserted_id)
    

  
  
**Storing in MongoDB**  
In my [Jupyter notebook example](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/FitsHeaders_MongoDB.ipynb), I've loaded a handful of FITS files that are readily accessible on the web. An astronomer actively carrying research or development can easily have hundreds of these files spread all over their directories. By storing them in MongoDB, we get to use database queries to quickly search through all of them without having to remember the directory structure or, if we are clever with the indexing, with how the headers are defined. This is an important point as even though FITS headers contain standard keywords, many of them are up to user choice. So some telescopes may store filter information (as an example) as FILTER while others may use FILTNAME. To alleviate this matter, I created a text index across *all* text fields.  
  

    
    
    collection.create_index([('$**', pymongo.TEXT)], 
                                                     name='text_fields', 
                                                     background=True)
    

  
With this index, I can readily query without having to know the FITS keywords themselves:  
  

    
    
    cursor = collection.find({'$text': {'$search': 'OG590 F673N'}}, 
                             {'_id': 0, 'FILENAME': 1, 'FILTER': 1, 
                              'FILTNAM1': 1, 'FILTNAM2': 1})
    for doc in cursor:
        print(doc)
    
    # Output
    {'FILTNAM1': 'F673N', 'FILTNAM2': '', 'FILENAME': 'vtest3.fits'}
    {'FILTER': 'OG590', 'FILENAME': 'HorseHead.fits'}
    

  
Additional fields could be created when inserting/updating data in the database to store frequently used information. In the example above, I stored the filename under the keyword FILENAME regardless of whether it was in the header or not. I could also store the file path or file size, or even derived information if I wanted to actually look at the data and store some value from it.  
**  
** **The alternative: CAOM**  
At [MAST](https://mast.stsci.edu/), where I currently work, we store metadata for a lot of different telescopes and missions. However, we don't actually use MongoDB at the moment, instead relying on more standard relational database systems. The metadata is still very varied, though, so the way we consolidate everything together is by utilizing the Common Archive Observation Model, or [CAOM](http://www.opencadc.org/caom2/). This was originally developed by the [Canadian Astronomical Data Centre](http://www.cadc-ccda.hia-iha.nrc-cnrc.gc.ca/en/) and has since been adapted at several archives around the world. The way it works is that one maps out the original metadata from the FITS file (or whatever format you're using) to the CAOM model and then store that instead. Although some minor information can be 'lost in translation' the model is actually very robust and has the benefit of providing a very standard way for us to index our tables and construct optimized queries.  
  
**Conclusion**  
In the end, it's up to the individual user how they proceed in storing their metadata, ranging from a simple list on paper, to a table of filenames, to a document-store NoSQL database, or to a model like CAOM. In this post, I've gone over how one can store FITS metadata in MongoDB, which I feel is surprisingly simple and a powerful way to store such information. In future posts, I'll explore ways to search for data stored in MongoDB with one of the more important ways to search for astronomical data- the cone search. 
