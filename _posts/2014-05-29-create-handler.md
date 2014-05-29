---
layout: page
title: "Add Handler"
category: party
date: 2014-05-29 19:55:23
order: 2
---

#### Registering New Task Handler

Create a new file in party/tasks/ call it sample.py

Tasks must be inherited from TaskBase and the logic is executed inside the run method.

Attributes available in the lifetime of a Task:

* self._id : (24digit string) ID of the current task.
* self.timestamp : (timestamp w/o timezone) Timestamp at this Task was scheduled. One could use system_now() but for micro-precision tasks like Billing its important to maintain the start time of the task.
* self.last_run_on : (timestamp w/o timezone) Last execution time of this Task. Set only if its a Recurring Task.
* self.attempts : (integer) Execution attempt. Defaults to 1 unless the task has failed previously.
* self.is_recurring : (boolean) Set only if its a recurring task.
* self.data : (dictionary) Any data that may have been passed to this Task at the time of Creation.

A sample Task Handler would look like this:

	class SampleTask(TaskBase):
	    def run(self):
	        print self.data.get("arg1")
	        print "Attempting run %s of Tasl: %s which was scheduled on %s." % (
	            self.attempts,
	            self._id,
	            self.timestamp
	        )

#### Recurring Tasks

Tasks are usually one-off but some cases like weekly reminders or monthly bills require tasks to be executed periodically. Recurring tasks are an extension of the regular task by over-riding the on_complete method. Tasks expose a schedule_next_run method, which can be called with minutes or timestamp argument to re-schedule it in future.

Avoid Recursion

	If you override the on_complete method of a task and do not assign it a new timestamp, you could leave a Task in an infinite recursion. Use this cautiously and only when needed.

Singleton

	You can have *1 and only 1* recurring task for the same (TaskHandler + TaskData). An attempt to insert duplicate tasks would raise a TaskException citing the same reason.

Example of a Task running every 30 minutes

	class RecurringTask(TaskBase):
	   is_recurring = True

	   def run(self):
	       #Perform something
	       pass

	   def on_complete(self, tasks):
	       self.schedule_next_run(minutes=30)

Example of a Task being executed every 30 days

	from datetime import timedelta

	class RecurringTask2(TaskBase):
	   is_recurring = True

	   def run(self):
	       #Perform something
	       pass

	   def on_complete(self, tasks):
	       self.schedule_next_run(timestamp=self.timestamp+timedelta(days=30))