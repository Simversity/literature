---
layout: page
title: "Add Tasks"
category: party
date: 2014-05-29 19:56:12
order: 3
---

Add new Tasks to Job-Queue
--------------------------

Party exposes a HTTP interface which can be used to interact with the Job Queue.

A task input must be of the following format:
	{
	    'party': <String> Required //Name of the TaskHandler
	    'data': <Dictionary> Optional //Additional Data for Task
	    'timestamp': <datetime object> Optional //Timestamp when this task should be executed, system_now() by default
	}

Since the timestamp field is optional, the default behavior is to queue the Task for immediate execution in the next available slot.

	from simtools.uhttpclient import BlockingHTTPClient

	con = BlockingHTTPClient("/tmp/party.sock") #If listening on a Unix Domain Socket.
	con = BlockingHTTPClient("127.0.0.1:8032") #If listening on a TCP Port.

You can add a new task to the jobQueue using a POST request.

	data = {"tasks": [
	  {
	    "party": "SampleTask",
	    "data": {"arg1": "val1"},
	    "timestamp": datetime.datetime(2014, 03, 11, 05, 12, 22, 86400)
	  },
	]}

	con("/tasks", None, data=data, method="POST")

Tasks accepts a list, so you can add multiple tasks in the same POST request.

	data = {"tasks": [
	  {
	    "party": "SampleTask",
	    "data": {"arg1": "val1"},
	    "timestamp": datetime.datetime(2014, 03, 11, 05, 12, 22, 86400)
	  },
	  {
	    "party": "SampleTask2",
	    "data": {"arg1": "val1"}, //Do not Provide timestamp.
	  },
	  {
	    "party": "SampleTask2",
	    "data": {"arg1": "val1"},
	    "timestamp": datetime.datetime(2014, 03, 11, 05, 12, 22, 86400) //With Timestamp
	  },
	]}

	con("/tasks", None, data=data, method="POST")

The request returns a list of all the tasks that were added to the Queue.