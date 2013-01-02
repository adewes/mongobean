#Introduction

*mongobean* is a tiny, easy-to-use object wrapper for MongoDB. It provides classes that wrap documents, collections and cursors
and makes it easy to work with them.

#Usage

Just download the source files and put them where Python finds them. To create a new document, just inherit from the *Document* class:
```python
import mongobean.orm as orm

class Restaurant(orm.Document):
   pass

class Address(orm.Document):
  pass
```

##Storing documents

Working with documents is really easy. Following our example, you can just create new *Restaurant* object, store data in it (including references to other documents) and store it in the database:
```python
restaurant = Restaurant()
restaurant['name'] = u'Luigis'

address = Address(street = 'Main avenue',number = 24, zip = '11111',town = 'Onetown')

restaurant['address'] = address

restaurant.save()
```
This code will create a restaurant and an address object, assigns the address to the restaurant and stores both documents in the database, resulting in the following JSON:
```json
//Collection: 'restaurant'
{u'_created_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'_updated_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'_id': ObjectId('50e42643421aa93af22942c5'), u'name': u'Luigis', u'address': {u'_type': u'Address', u'_id': ObjectId('50e42643421aa93af22942c6')}}
//Collection: 'address'
{u'city': u'Onetown', u'_updated_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 413000), u'zip': u'11111', u'number': 24, u'_created_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 412000), u'street': u'Main avenue', u'_id': ObjectId('50e42643421aa93af22942c6')}
```
Behind the scenes, mongobean automatically defines collections and types for all classes inheriting from the *Document* class. By default, the name of the collection is the given as the class name in lowercase.
For each class, the database and collection can be changed by setting the "_collection_name" and "_database" class variables.

##Loading documents

To load a document of a given type, you can use the *collection* class variable, which returns a wrapper around the pymongo *Collection* class and provides *find* and *find_one* functions that return valid documents:
```python
restaurant = Restaurant.collection.find_one({'name' : 'Luigis'})
print restaurant
#This will print: Restaurant(**{u'_created_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'_updated_at': datetime.datetime(2013, 1, 2, 13, 21, 23, 403000), u'name': u'Luigis', u'address': LazyAddress(**{'_id': ObjectId('50e42643421aa93af22942c6')}), u'_id': ObjectId('50e42643421aa93af22942c5')})
restaurant['address']['street']
#This will result in another DB query and will print: u'Main avenue'
```

As you can see, nested subdocuments are automatically loaded using the correct document class. Please note that the loading is *lazy* by default, i.e. the subdocuments only get loaded from the database when one of their attributes get requested, which reduces loading time and elegantly avoids problems with self-referencing documents. 

#Further reading

At the moment there is no further documentation available, so just have a look at the source code, it's really short and easy to understand. The code is "public domain", so feel free to use and modify it for your own projects!
