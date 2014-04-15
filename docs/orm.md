Introduction
------------

NoSQL databases like MongoDB are a hot tool these days. The ease of usage and the speed of development make them the right choice of tool for most of application use cases. The fact that you can dump any JSON (read BSON) data to databases is the real advantage over tight physical schemas. But the lack of data integrity and validations could mean that all the validations and datatype checks have to re-implemented at the application layer.

Simtools' ORM is a lightweight ORM available to perform basic data type checks and constraint validations. It can be imported to create a base class used to define models that represent a collection in the database. ORM also provides a bunch of default datatypes that can be used alongside any custom datatypes that you may want to provde.

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

```python
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
Returns MongoCuror instance.

Definition
```python
get_many(filter_args=None, limit=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)
```

Example
```python
user_cursor = User.get_many({"lastname": "Jack"}, limit=10, fields=["_id", "firstname", "email"])

user_cursor_two = User.get_many({"lastname": "Jack"}, limit=10, fields=["_id", "firstname", "email"], gender="male")
```

###get_one
Returns Single Mongo Document.

Definition
```python
get_many(filter_args=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)
```

Example
```python
alpha_user = User.get_one({"email": "alpha@romeo.com"}, fields=["_id", "firstname", "email"])
```

###count
Returns Total no. of Documents that match the query.
Its effectively an alias for get_many().count() Only much faster, since it does not perform cursor transfer over the wire and only uses the _id field which has a very good likelihood of being present in RAM.

Example
```python
user_count = User.count({"lastname": "Jack"})
```

###remove
Remove all the documents in the collection that match the query criteria

Definition:

```python
remove(filter_args)
```

filter_args could be

* List of _ids that need to deleted.
* A Dictionary which matches all the documents.
* A single _id

Example

Deleting a list of Users
```python
User.remove(["1234567725352532USEER", "1234564646444652532USEER"])
```
OR Deleting All Users with lastname Townsend
```python
User.remove({"gender": "townsend"})
```
OR Deleting a single User
```python
User.remove("1234567725352532USEER")
```

###update
Update a bulk of documents matching the query, with a update specifier

*  Upsert is blocked. Refer to https://github.com/Simversity/blackjack/issues/1051
*  Writes are safe by default.
*  Multi is enabled by default.

Definition
```python
update(filter_args, update_spec)
```

Example
```python
User.update({"lastname": "martin"}, {"$set": {"lastname": "Jordan"}})
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
