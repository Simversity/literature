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

* \__tablename__ : collection name of this model.
* \__rid__ : unique 5-character string identifier representing this model. It helps collision at the time of ID generation.
* classmethod - using() : Returns a connection to the database housing the collection.

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
Returns a single Mongo document.

Definition
```
get_many(filter_args=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)
```

Example
```
alpha_user = User.get_one({"email": "alpha@romeo.com"}, fields=["_id", "firstname", "email"])
```

###count
Returns number of documents that match the query.
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
Update a bulk of documents matching the query, with a update specifier.

Things to remember while updating:

*  All Upsert operations are blocked. Refer to [Issue 1051](https://github.com/Simversity/blackjack/issues/1051) for details.
*  Writes are safe by default. The default write_concern is 'w'.
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
Insert one or many documents into a collection. Always returns a List of 1 or more _ids that were inserted.

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
    {"hello": "world2"}
])
```

##Hooks

Hooks are callbacks that are triggered at different stages of the data flow.
ORM exposes a bunch of hooks that you can override that can transform the data being sent to the database.
All of these hooks are classmethods until mentioned otherwise, So please do not forget to use the @classmethod decorator.
Hooks can also be used for other purposes like logging / notifications / Task scheduling etc.

###prepare_delete_document
Delete is a 2-stage process. Documents are never really deleted and marked with {"deleted": True}.
Application developer is responsible for deleting these documents separately using a Cron-Job.
This is done so by-design since each document delete is responsible for all dependant relations to be deleted as well, something like CASCADE DELETE in SQL databases.
But that could take a long time to process, hence the quickest way to do this is mark the current document as {"deleted": True} and delete the dependencies separately.
Once marked as deleted, ORM will not fetch them in any subsequent query.
You can define the prepare_delete_document to add any more information to the delete spec.

Definition
```
@classmethod
prepare_delete_document(cls, delete_spec)
```

###prepare_update_document
The method is executed just before update is performed.

Definition:
```
@classmethod
prepare_update_document(cls, update_spec)
```

###prepare_insert_document
This method is executed just before an insert is performed.
The document argument recieved by this method is the same as one supplied to the insert method
i.e If you are inserting a single document, it would be a single document, whereas if you are inserting a list of documents this argument would be a list.

Definition:
```
@classmethod
prepare_insert_document(cls, document)
```

###prepare_update_query
This method is trigerred after the update query has been finalized. You can override this method to introduce any application specific query parameters.

Definition:
```
@classmethod
prepare_update_query(cls, filter_args)
```

###prepare_get_query
This method is trigerred after the get query has been finalized.
This is executed for all the 3 get operations:

* get_many
* get_one
* count

You can override this method to introduce any application specific query parameters.

Definition:
```
@classmethod
prepare_update_query(cls, filter_args)
```

###on_insert
The on_insert hook is called AFTER the document(s) have been inserted into the collection. It is called with list of 1 or many _ids that were returned by the insert operation.

Definition:
```
@classmethod
on_insert(cls, ids)
```

###on_update
The on_update hook is called BEFORE the actual update operation. You can use this method to record any changes in the watched documents. Please Note that this method is not very performance friendly and shall eventually be moved to Oplog scanners.

Definition:
```
@classmethod
on_update(cls, filter_args, document, updated_fields=None)
```

###on_delete
This is the worst performnig hook of all. Once overridden it is called with a list of all documents that are about to be deleted.
This is a *really slow* hook because:

1. All the documents to be deleted are fetched.
2. The cursor is expanded & flattened to a List. So you end up carrying the entire list of documents in memory.

Despite its awful performance the Hook can come really handy to keep a log/backup of all documents that were to be deleted.

Defintiion:
```
@classmethod
on_delete(cls, documents)
```
