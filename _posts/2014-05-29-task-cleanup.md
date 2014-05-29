---
layout: page
title: "Task Cleanup"
category: party
date: 2014-05-29 19:56:38
order: 6
---

A bare minimum task cosumes approximately 400 bytes of space. A collection of 100,000 such tasks would consume *(100,000 x 400)/(1024 x 1024)* = 40 MB of space.
Assume thats the number of tasks being processed each day, it can lead to *1200 MB* of data over a month. It is important to purge old tasks that have completed or failed.

Party exposes a TaskCleanup task that runs every 1 hour and does the cleanup.
Add a new task using one of the above methods to kickstart the process.

	data = {"tasks": [{"party": "TaskCleanup"}]}
	con("/tasks", None, data=data, method="POST")

The cleanup method prefers to dump the qualifying tasks to a flat file. However, if the path is not provided or invalid, dumps are sent to completed_tasks & failed_tasks collection in party3 database. Not having a valid dump path is less effcient as it does not free up the database space and just moves data from one collection to another. This is done to speed up the operations on the Main tasks collection. Dump files are rotated for every 5 MB of data and to a maximum of 20 files.

In party/local_settings.py set COMPLETED_LOG_PATH & FAILED_LOG_PATH parameters to point to the files where the tasks would be dumped.