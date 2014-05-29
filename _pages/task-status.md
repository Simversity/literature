---
layout: page
title: "Task Status"
category: party
date: 2014-05-29 19:56:47
order: 7
---

Issue a GET request to fetch the current status of the party server.

Response is a JSON map returning data that looks like this:

	{
	    "recurring": [],
	    "completed": "Task Logger unavailable. Please add TaskCleanup to Party",
	    "failed": [],
	    "total_workers": 0,
	    "debug": true,
	    "undumped": [ ... {
	        "count": {TotalCompletedTasks},
	        "_id": {TaskName}
	    } ... ],
	    "last_run_on": "Unavailable",
	    "pending": [ ... {
	        "count": {TotalPendingTasks},
	        "_id": {TaskName},
	        "upcoming": {UpComingTaskID}
	    } ... ],
	    "restarted_workers": "Unavailable"
	}