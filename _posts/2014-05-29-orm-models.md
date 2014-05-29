---
layout: page
title: "Models"
category: orm
date: 2014-05-29 21:35:36
order: 3
---

#### Models

A model is a collection of fields which are instances of Datatypes.
Models need to declare

* \__tablename__ : collection name of this model.
* \__rid__ : unique 5-character string identifier representing this model. It helps collision at the time of ID generation.
* classmethod - using() : Returns a connection to the database housing the collection.

Sample Model definition that uses a bunch of Datatypes

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

##Model Operations

###get_many
Returns MongoCursor instance.

Definition

	get_many(filter_args=None, limit=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)

Example

	user_cursor = User.get_many({"lastname": "Jack"}, limit=10, fields=["_id", "firstname", "email"])

	user_cursor_two = User.get_many({"lastname": "Jack"}, limit=10, fields=["_id", "firstname", "email"], gender="male")

####get_one

Returns a single Mongo document.

Definition

	get_many(filter_args=None, skip=0, sort=-1, sortkey='_id', max_scan=None, fields=None, **kw_query)

Example

	alpha_user = User.get_one({"email": "alpha@romeo.com"}, fields=["_id", "firstname", "email"])

###count

Returns number of documents that match the query.
Its effectively an alias for get_many().count(); only much faster since it does not perform cursor transfer over the wire and uses just the _id field which has a good likelihood of being served out of RAM.

Example

	user_count = User.count({"lastname": "Jack"})


###remove
Remove all the documents in the collection that match the query criteria

Definition:

	remove(filter_args)

filter_args could be

* List of _ids that need to deleted.
* A Dictionary which matches all the documents.
* A single _id

Example

Deleting a list of Users

	User.remove(["1234567725352532USEER", "1234564646444652532USEER"])

OR Deleting All Users with lastname Townsend

	User.remove({"gender": "townsend"})

OR Deleting a single User

	User.remove("1234567725352532USEER")

###update
Update a bulk of documents matching the query, with a update specifier.

Things to remember while updating:

*  All Upsert operations are blocked. Refer to [Issue 1051](https://github.com/Simversity/blackjack/issues/1051) for details.
*  Writes are safe by default. The default write_concern is 'w'.
*  Multi is enabled by default.

Definition

	update(filter_args, update_spec)

Example
	
	User.update({"lastname": "martin"}, {"$set": {"lastname": "Jordan"}})

###insert

Insert one or many documents into a collection. Always returns a List of 1 or more _ids that were inserted.

Definition:

	insert(documents)

Example

	SampleCollection.insert({"hello": "world"})
	OR
	SampleCollection.insert([
    	{"hello": "world"},
    	{"hello": "world2"}
	])