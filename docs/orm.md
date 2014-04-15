Introduction
------------

NoSQL databases like MongoDB are a hot tool these days. The ease of usage and the speed of development make them the right choice of tool for most of application use cases. The fact that you can dump any JSON (read BSON) data to databases is the real advantage over tight physical schemas. But the lack of data integrity and validations could mean that all the validations and datatype checks have to re-implemented at the application layer.

Simtools' ORM is a lightweight ORM available to perform basic data type checks and constraint validations. It can be imported to create a base class used to define models that represent a collection in the database. ORM also provides a bunch of default datatypes that can be used alongside any custom datatypes that you may want to provde.

In a regular use case you would create a new collection and store stuff as:
```
collection = pymongo.MongoClient().sample_database.sample_coll
```

If you have already read the party document, you would be using dbapi instead of the MongoClient approach. Something like:
```
collection = dbapi(dbapi.sample_database, "sample_coll")
```

A regular update operation would look like
```
collection.insert({"data": "hello", "data_list": [1]})
collection.update({"data": "hello"}, {"$set": {"data_set": 2}})
```

If you notice, the data type of data_list has changed from a list to a string withour any errors.
Ideally you would like an error to be raised but pyMongo would not do that. Simtools' ORM solves this for you.

A quick re-implementation of the above requirement will looks like this.

```
from simtools.orm.base import ModelBase
from simtools.orm.datatypes import Unichar, List

class SampleModel(ModelBase):
    __tablename__ = "sample_coll"
    __rid__ = "SAMPL"

    data = Unichar(nullable=False)
    data_list = List(default=[], nullable=False)

    @classmethod
    def using(cls):
        return pymongo.MongoClient().sample_database


sm = SampleModel.insert({"data": "hello", "data_list": [1]})

SampleModel.update({"data": "hello"}, {"$set": {"data_list": 2}})

```

This would duly raise an error which says that datatype does not match. How wonderful !!

DataTypes
---------

All datatypes inherit from simtools.DataType and has following attributes

* default - Default value of the datatype in case the field is absent in the Model.
* nullable {Boolean} - If the datatype can accommodate null values.
* humanize(value) {method} - Transforms the incoming BSON value from DB
* dbfy(value) {method} - Transform the value before sending to DB

Available datatypes in simtools.datatypes

* Unichar - For all text/string purposes
* Regex - Inhertis Unichar but value must match the expression
* ID - Should be a 24 digit string
* Email
* URL
* Boolean
* Integer
* Decimal
* Currency
* Html
* List
* Dict
* Timestamp - Milliseconds since epoch
* Datemonth - Integer ranging from 1 - 12
* Dateday - Integer ranging from 1 - 31
* Timehour - Integer ranging from 0 - 24
* Timeminutes - Integer ranging from 0 - 60


Models
------

A model is a collection of fields which are instances of Datatypes.
Models need to declare

* __tablename__ collection name of this model.
* __rid__ a 5-digit unique identifier for this model. It helps collision at the time of ID generation.
* classmethod - using() Returns a connection to the database housing the collection.

Sample Model definition that uses a bunch of Datatypes

```
class User(ApiModelBase):
    __rid__ = '63702'

    @classmethod
    def using(cls):
        conn = pymongo.MongoClient()
        return conn.test_db

    firstname = Unichar(nullable=False)
    middlename = Unichar()
    lastname = Unichar(default="Last Name Unknown")
    username = Unichar(nullable=True)

    email = Email(nullable=False)
    password = Unichar(nullable=False)

    timezone = Unichar(default=TZ)

    last_access_on = List()
    sex = Unichar(default="female")

    is_temp_password = Boolean()
    is_active = Boolean(default=True)
    is_admin = Boolean(default=False)
    visibility = Dict()
```

##Model Operations

###get_many
Returns MongoCursor instance.

Definition
```
get_many(filter_args=None, limit=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)
```

Example
```
user_cursor = User.get_many({"lastname": "Jack"}, limit=10, fields=["_id", "firstname", "email"])

user_cursor_two = User.get_many({"lastname": "Jack"}, limit=10, fields=["_id", "firstname", "email"], gender="male")
```

###get_one
Returns Single Mongo Document.

Definition
```
get_many(filter_args=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)
```

Example
```
alpha_user = User.get_one({"email": "alpha@romeo.com"}, fields=["_id", "firstname", "email"])
```

###count
Returns Total no. of Documents that match the query.
Its effectively an alias for get_many().count(); only much faster since it does not perform cursor transfer over the wire and uses just the _id field which has a good likelihood of being served out of RAM.

Example
```
user_count = User.count({"lastname": "Jack"})
```

###remove
Remove all the documents in the collection that match the query criteria

Definition:

```
remove(filter_args)
```

filter_args could be

* List of _ids that need to deleted.
* A Dictionary which matches all the documents.
* A single _id

Example

Deleting a list of Users
```
User.remove(["1234567725352532USEER", "1234564646444652532USEER"])
```
OR Deleting All Users with lastname Townsend
```
User.remove({"gender": "townsend"})
```
OR Deleting a single User
```
User.remove("1234567725352532USEER")
```

###update
Update a bulk of documents matching the query, with a update specifier

*  Upsert is blocked. Refer to https://github.com/Simversity/blackjack/issues/1051
*  Writes are safe by default.
*  Multi is enabled by default.

Definition
```
update(filter_args, update_spec)
```

Example
```
User.update({"lastname": "martin"}, {"$set": {"lastname": "Jordan"}})
```

###insert
Insert one or many documents into a collection. Always returns a List of _ids that were inserted.

Definition:
```
insert(documents)
```

Example
```
SampleCollection.insert({"hello": "world"})

OR

SampleCollection.insert([
    {"hello": "world"},
    {"hello": "world2}
])
```

##PerObject Operations
###save

###delete


##Hooks

###pre_save

###post_save

###prepare_save_document

###prepare_delete_document

###prepare_update_document

###prepare_insert_document

###prepare_update_query

###prepare_get_query

###on_insert

###on_delete

###on_update
