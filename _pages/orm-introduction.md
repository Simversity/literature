---
layout: page
title: "Introduction"
category: orm
date: 2014-05-29 21:35:08
order: 1
---

#### Introduction

NoSQL databases like MongoDB are a hot tool these days. The ease of usage and the speed of development make them the right choice of tool for most of application use cases. The fact that you can dump any JSON (read BSON) data to databases is the real advantage over tight physical schemas. But the lack of data integrity and validations could mean that all the validations and datatype checks have to re-implemented at the application layer.

Simtools' ORM is a lightweight ORM available to perform basic data type checks and constraint validations. It can be imported to create a base class used to define models that represent a collection in the database. ORM also provides a bunch of default datatypes that can be used alongside any custom datatypes that you may want to provde.

In a regular use case you would create a new collection and store stuff as:

	collection = pymongo.MongoClient().sample_database.sample_coll


If you have already read the party document, you would be using dbapi instead of the MongoClient approach. Something like:

	collection = dbapi(dbapi.sample_database, "sample_coll")

A regular update operation would look like

	collection.insert({"data": "hello", "data_list": [1]})
	collection.update({"data": "hello"}, {"$set": {"data_set": 2}})


If you notice, the data type of data_list has changed from a list to a string withour any errors.
Ideally you would like an error to be raised but pyMongo would not do that. Simtools' ORM solves this for you.

A quick re-implementation of the above requirement will looks like this.

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

This would duly raise an error which says that datatype does not match. How wonderful !!
