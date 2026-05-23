---
layout: post
read_time: true
show_date: true
title: "Data Science: Python Dataclasses and MongoDB"
date: 2019-05-18
image: /assets/img/posts/2019/20190518/mongodb_compass_1.png
tags: [Data Science, Brown Dwarfs, Databases, NoSQL, Astronomy, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

Over the past few weeks, I've been playing a bit with some NoSQL databases, in particular, with MongoDB. This is one particular type of database known as a document-store database and it works primarily by saving JSON formatted 'documents'. While exploring this technology and working on some Python code, I realized how easy it is to convert a standard Python class into a dictionary and how dictionaries readily translate into JSON. With this knowledge in hand, a light-bulb went off in my head as I realized I could make use of the new dataclasses implemented as part of Python 3.7 and quickly create a working database with minimal code.  
  
In this post, I'll describe some of the ideas I had in mind while working through this and, if you want to try this on your own, I can point you to [this Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/DataClass_MongoDB.ipynb) where I work out this example.  
  
**Python Dataclasses**  
In Python 3.7, the concept of [dataclasses ](https://docs.python.org/3/library/dataclasses.html)was introduced. As I understand them, this is a way to quickly create Python classes with a number of preset functionality built in. This makes the process easier and faster as you don't need to worry about any details that could go into an __init__ or __repr__ method (unless you want to of course). For a beginner, or for any user who just wants to get started quickly, this can be very powerful.  
  
In this example, I create a fairly simple dataclass to store information about brown dwarfs, a type of astronomical object that is not big enough to burn its fuel like a star, but larger than a planet. They are interesting objects in their own right, and I've worked with the BDNYC team to help them grow and develop [their database](http://database.bdnyc.org/query) of these objects.  
  
Here's the code I used:  

    
    
    import json
    from dataclasses import dataclass, field
    
    @dataclass
    class Coords:
        ra: float
        dec: float
            
    @dataclass
    class Photometry:
        value: float
        error: float
        unit: str = 'mag'
            
    @dataclass
    class BrownDwarf:
        source_id: int
        name: str
        coords: Coords
        J: Photometry = None
        H: Photometry = None
        Ks: Photometry = None
        spectral_type: str = None
        name_list: list = field(default_factory=list)
            
        def to_json(self):
            return json.dumps(self.__dict__, default=lambda x: x.__dict__, indent=4)
    

  
In this case, I actually created 3 dataclasses: one for the coordinates, one for any photometry values, and one to store all this together. Making these nested classes is surprisingly simple.  
Here's how you could load one with data, in this case for TWA 27:  

    
    
    c = Coords(ra=181.889, dec=-39.548)
    # 2MASS_J: 12.99 +/- 0.03
    # 2MASS_H: 12.39 +/- 0.03
    # 2MASS_Ks: 11.95 +/- 0.03
    bd = BrownDwarf(source_id=11, name='1207-3932', coords=c,
                   J = Photometry(12.99, 0.03),
                   H = Photometry(12.39, 0.03),
                   Ks = Photometry(11.95, 0.03))
    bd.name_list = ['TWA 27', '2MASS J12073346-3932539']
    bd.spectral_type = 'M8.0'
    print(bd)
    
    # Output
    BrownDwarf(source_id=11, name='1207-3932', 
    coords=Coords(ra=181.889, dec=-39.548), 
    J=Photometry(value=12.99, error=0.03, unit='mag'), 
    H=Photometry(value=12.39, error=0.03, unit='mag'), 
    Ks=Photometry(value=11.95, error=0.03, unit='mag'), 
    spectral_type='M8.0', 
    name_list=['TWA 27', '2MASS J12073346-3932539'])
    

  
**Moving to JSON**  
There are many ways we could represent dataclasses. The one I want to touch on is JSON since, as you'll see shortly, that allows me to play with a particular NoSQL database implementation. Fortunately for us, it's not too hard to convert our newly created dataclass into JSON. For that we use json package and rely on the similarity between Python dictionaries and JSON.  
  
To convert a class to a dictionary you can use the built-in method __dict__ or the dataclasses.asdict function. However, as you may have noticed in my example code above, I included a function in my BrownDwarf dataclass to output itself to JSON. This is because I wanted to specify that, as the code serializes the dataclass, if it doesn't encounter something it knows about already it should use the object's__dict__ method to store that representation. Effectively, what this means is that any nested dataclasses are also represented as dictionaries and therefore nicely formatted in JSON.  
  
Here's how our BrownDwarf looks like in JSON:  

    
    
    print(bd.to_json())
    
    # Output
    {
        "source_id": 11,
        "name": "1207-3932",
        "coords": {
            "ra": 181.889,
            "dec": -39.548
        },
        "J": {
            "value": 12.99,
            "error": 0.03,
            "unit": "mag"
        },
        "H": {
            "value": 12.39,
            "error": 0.03,
            "unit": "mag"
        },
        "Ks": {
            "value": 11.95,
            "error": 0.03,
            "unit": "mag"
        },
        "spectral_type": "M8.0",
        "name_list": [
            "TWA 27",
            "2MASS J12073346-3932539"
        ]
    }
    

  
**MongoDB**  
As I briefly mentioned, MongoDB is a NoSQL database, but don't let that scare you. A NoSQL database is just a type of database where the data is stored in a manner that's somewhat different than a typical Relational Database System such as SQL. A simple example is the key-value store, where keys are stored with associated values. If that sounds familiar, it should- it's effectively the same as a Python dictionary. MongoDB is one level above that in that it stores documents built out of key-value pairs. This gives a bit of organizational logic to the whole thing and makes it very easy to understand compared to some other approaches out there.  
  
When we convert our dataclass into a JSON object, that effectively serves as a document for storing in any way we like. This JSON object contains all the information we wanted to store in our dataclass so, if designed properly, it's a complete representation of an astrophysical object. One could consider each dataclass its own separate document, so you could store Photometry separate from the BrownDwarf itself, but I've kept things simple by just sending the whole object together- effectively [embedding documents](https://docs.mongodb.com/manual/core/data-model-design/#data-modeling-embedding) inside another document.  
  
To install a MongoDB server and start it up, or to connect to an existing server, I'll refer you to the [MongoDB documentation](https://docs.mongodb.com/manual/installation/). My code example establishes a connection to the default localhost, which is a locally running MongoDB server. This is usually good enough to test things out. Once you have a connection established you can create a database and collection to store your documents. To translate MongoDB-speak to something more [SLQ-like](https://docs.mongodb.com/manual/reference/sql-comparison/): a collection is effectively a table and a document is effectively a row in that table. Because NoSQL databases like MongoDB do not need schemas defined ahead of time, you actually create the database and collection automatically when you load up a document to it. Check it out:  
  

    
    
    import pymongo
    
    client = pymongo.MongoClient()  # default connection (ie, local)
    
    db_name = 'test'
    db = client[db_name]  # database
    dwarfs = db.dwarfs
    dwarfs.drop()  # drop collection, if needed
    
    json_data = json.loads(bd.to_json())
    result = dwarfs.insert_one(json_data)
    
    # Quick check to confirm load
    cursor = dwarfs.find({'source_id': 11})
    for doc in cursor:
        print(doc)
    
    # Output
    {'_id': ObjectId('5cdff0f4244648147014faa1'), 'source_id': 11, 
    'name': '1207-3932', 'coords': {'ra': 181.889, 'dec': -39.548}, 
    'J': {'value': 12.99, 'error': 0.03, 'unit': 'mag'}, 
    'H': {'value': 12.39, 'error': 0.03, 'unit': 'mag'}, 
    'Ks': {'value': 11.95, 'error': 0.03, 'unit': 'mag'}, 
    'spectral_type': 'M8.0', 'name_list': ['TWA 27', '2MASS J12073346-3932539']}
    

  
**Putting it all Together**  
Once you load up some more objects (see examples in the [Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/DataClass_MongoDB.ipynb)) you can start playing around with some quick database queries. The syntax is different than SQL, but nevertheless quite powerful. You can also create indices for faster queries and do aggregations, which is a sort of pipeline where you can run a sequence of queries or commands together to get more advanced results. I personally like to use the [Compass ](https://docs.mongodb.com/compass/current/)program to connect to my MongoDB database and explore its contents. I believe some advanced queries aren't supported in it, but it still gives you a good overview to test how things look:  
  
![](/assets/img/posts/2019/20190518/mongodb_compass.png)  
---  
Compass application showing a query on my MongoDB Brown Dwarf test database (click to see larger version).  
  
There are tons of queries you could possibly create; [my Jupyter notebook](https://github.com/dr-rodriguez/BlogTutorials/blob/master/notebooks/DataClass_MongoDB.ipynb) only briefly touches on some of them. My knowledge of how to query MongoDB databases is still pretty limited, so I'll just point you to [the documentation](https://docs.mongodb.com/manual/contents/) again for more examples.  
  
I have several ideas for using this database technology and plan to write up a few more blog posts to describe what I do and provide more examples. That'll take some time though, since I first have to prototype and test if my ideas will work at all! Still, I hope this has been informative and encourages you to explore both Python dataclasses and NoSQL databases. 
