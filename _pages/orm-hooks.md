---
layout: page
title: "Hooks"
category: orm
date: 2014-05-29 21:35:40
order: 4
---

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

	@classmethod
	prepare_delete_document(cls, delete_spec)

###prepare_update_document
The method is executed just before update is performed.

Definition:

	@classmethod
	prepare_update_document(cls, update_spec)

###prepare_insert_document
This method is executed just before an insert is performed.
The document argument recieved by this method is the same as one supplied to the insert method
i.e If you are inserting a single document, it would be a single document, whereas if you are inserting a list of documents this argument would be a list.

Definition:

	@classmethod
	prepare_insert_document(cls, document)

###prepare_update_query
This method is trigerred after the update query has been finalized. You can override this method to introduce any application specific query parameters.

Definition:

	@classmethod
	prepare_update_query(cls, filter_args)


###prepare_get_query
This method is trigerred after the get query has been finalized.
This is executed for all the 3 get operations:

* get_many
* get_one
* count

You can override this method to introduce any application specific query parameters.

Definition:

	@classmethod
	prepare_update_query(cls, filter_args)


###on_insert
The on_insert hook is called AFTER the document(s) have been inserted into the collection. It is called with list of 1 or many _ids that were returned by the insert operation.

Definition:

	@classmethod
	on_insert(cls, ids)


###on_update
The on_update hook is called BEFORE the actual update operation. You can use this method to record any changes in the watched documents. Please Note that this method is not very performance friendly and shall eventually be moved to Oplog scanners.

Definition:

	@classmethod
	on_update(cls, filter_args, document, updated_fields=None)


###on_delete
This is the worst performnig hook of all. Once overridden it is called with a list of all documents that are about to be deleted.
This is a *really slow* hook because:

1. All the documents to be deleted are fetched.
2. The cursor is expanded & flattened to a List. So you end up carrying the entire list of documents in memory.

Despite its awful performance the Hook can come really handy to keep a log/backup of all documents that were to be deleted.

Defintiion:

	@classmethod
	on_delete(cls, documents)