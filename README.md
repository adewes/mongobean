#Introduction

*mongobean* is a tiny, easy-to-use object wrapper for MongoDB. It provides classes that wrap documents, collections and cursors
and make it easy to work with them.

#Usage

Just download the source files and put them where Python can find them. To create a new document type, just inherit from the *Document* class:
```python
import mongobean.orm as orm

class Restaurant(orm.Document):
   pass

class Address(orm.Document):
  pass
```

##Creating and storing documents

Working with documents is really easy. Following our example, you can just create a new *Restaurant* object, put data in it (including references to other documents) and save it to the database:
```python
restaurant = Restaurant()
#The document instance can be used like a dict to get and set attributes:
restaurant['name'] = u'Luigis'

#Attributes can be specified when creating a new instance as well
address = Address(street = 'Main avenue',number = 24, zip = '11111',town = 'Onetown')

#Documents can contain references to other documents
restaurant['address'] = address

#This will save the restaurant and the address documents to the DB, automatically creating DB references where needed.
restaurant.save()
```
This code will create a restaurant and an address object, assign the address to the restaurant and store both documents in the database, resulting in the following JSON:
```json
//Collection: 'restaurant'
{u'_created_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'_updated_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'_id': ObjectId('50e42643421aa93af22942c5'), u'name': u'Luigis', u'address': {u'_type': u'Address', u'_id': ObjectId('50e42643421aa93af22942c6')}}
//Collection: 'address'
{u'city': u'Onetown', u'_updated_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 413000), u'zip': u'11111', u'number': 24, u'_created_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 412000), u'street': u'Main avenue', u'_id': ObjectId('50e42643421aa93af22942c6')}
```
Please note the automatic creation of timestamps and IDs. 

##Behind the scenes 

Mongobean automatically defines collections and types for all classes inheriting from the *Document* class. By default, the name of the collection is the given as the class name in lowercase. When loading documents, it looks up the document type (as given in the JSON document) in its registry and, if it matches a known type, initializes an instance of the appropriate type with the document data.

For each class, the database and collection names can be changed by setting the "_collection_name" and "_database" class variables. The default database connection for all document types can be changed by redefining the *default_db* function in the *mongbean.orm* module:

```python
import mongobean.orm as orm
import pymongo

class Restaurant(orm.Document):

   """
   Restaurant class with custom collection name and database connection.
   """

   _collection_name = "fancy_restaurant"
   _database = pymongo.MongoClient().fancy_restaurants

#Changing mongobean's default database connection:
orm.default_db = lambda: pymongo.MongoClient().my_fancy_database
```

##Loading documents

To load a document of a given type, you can use the *collection* class variable of the type's class, which returns a wrapper around the pymongo *Collection* class and provides *find* and *find_one* functions that return valid instances of the given document type:
```python
#Load a restaurant from the DB:
restaurant = Restaurant.collection.find_one({'name' : 'Luigis'})

print restaurant
#This will print: Restaurant(**{u'_created_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'_updated_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'name': u'Luigis', u'address': LazyAddress(**{'_id': ObjectId('50e42643421aa93af22942c6')}), u'_id': ObjectId('50e42643421aa93af22942c5')})

restaurant['address']['street']
#This will result in another DB query and will print: u'Main avenue'
```

As you can see, nested subdocuments are automatically loaded using the correct document class. Please note that the loading is *lazy* by default, i.e. the subdocument's attributes get fetched from the database only if needed, thereby reducing loading times and avoiding problems with self-referencing documents. 

#Further reading

I'm still working on a more thorough documentation, so for the moment just have a look at the code, it's really short and easy to understand. The code is "public domain", so feel free to use and modify it for your own projects!
