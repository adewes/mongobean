#Introduction

*mongobean* is a tiny, easy-to-use object wrapper for MongoDB. It provides classes that wrap documents, collections and cursors
and makes it easy to work with objects.

#Usage

Just download the source files and put them where Python finds them. To create a new document, just inherit from the *Document* class:
```python
import mongobean.orm as orm

class Restaurant(Document):
   pass

class Address(Document):
  pass
```
After this, you can just create new *Restaurant* objects, store data in them and store them to the dabase:
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
