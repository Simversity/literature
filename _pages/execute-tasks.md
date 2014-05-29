---
layout: page
title: "Execute Tasks"
category: party
date: 2014-05-29 19:56:16
order: 4
---

Based on the number of workers available, job scheduler fetches new tasks from the Queue and dispatches them for Execution.

Tasks with a lower timestamp are executed ahead of others. Scheudler is biased towards freshness and success, so if two tasks have the same timestamp one with lower attempts is preferred. When all other paramaters match, One that was creared earlier, is preferred.

Rules

* Task once Completed cannot be reexecuted.
* Task once Deleted, cannot be executed.
* Task cannot be executed after 6 failed attempts.

The execution process is automatic and is handled by a Job scheduler which can be started using a simple command

	python scheduler/

However, for the impatient developer there is a backdoor execution available. A POST request on /task will immediately execute the Task, irrespective of the Queue Status.

Do note that this is a blocking operation and should not be used in Production system as it can block the available processors on Party HTTP interface, which inturn can choke the inflow of tasks.

Sample Usage:

	con("/task/{TASK_ID}", None, method="POST")