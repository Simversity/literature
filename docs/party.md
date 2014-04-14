Introduction
------------
Party is an application framework designed to delegate work to other processes. It works on a post-fork model and spawns a group of workers, 1 per CPU core. It is used in a variety of applications and is one of the core components of our distributed processing architecture.

#### Use-Cases
Party can be used for a wide variety of tasks that are either Cron based, need to be processed on a future timestamp, need to perform CPU intensive work, require Batch/Data processing, or perform overnight Map Reduce on the production database.

Example

1. Send Email Notifications.
2. Processing Search Database updates.
3. Churn Forum & Feed based on Edge Rank algorithm.
4. Sending Weekly reminders.
5. Computing monthly Bills.
6. Encoding Videos.
7. Delete Orprhan data from the NoSQL database.
8. Update denormalized data periodically.
9. Schedule migrations to Reporting Database.

etc.

Registering New Task Handler
----------------------------

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

```python
class SampleTask(TaskBase):
    def run(self):
        print self.data.get("arg1")
        print "Attempting run %s of Tasl: %s which was scheduled on %s." % (
            self.attempts,
            self._id,
            self.timestamp
        )

```

#### Recurring Tasks

Tasks are usually one-off but some cases like weekly reminders or monthly bills require tasks to be executed periodically. Recurring tasks are an extension of the regular task by over-riding the on_complete method. Tasks expose a schedule_next_run method, which can be called with minutes or timestamp argument to re-schedule it in future.

Avoid Recursion:
> If you override the on_complete method of a task and do not assign it a new timestamp, you could leave a Task in an infinite recursion. Use this cautiously and only when needed.

Singleton:
> You can have *1 and only 1* recurring task for the same (TaskHandler + TaskData). An attempt to insert duplicate tasks would raise a TaskException citing the same reason.

Example of a Task running every 30 minutes

```python
class RecurringTask(TaskBase):
   is_recurring = True

   def run(self):
       #Perform something
       pass

   def on_complete(self, tasks):
       self.schedule_next_run(minutes=30)
```

Example of a Task being executed every 30 days

```python

from datetime import timedelta

class RecurringTask2(TaskBase):
   is_recurring = True

   def run(self):
       #Perform something
       pass

   def on_complete(self, tasks):
       self.schedule_next_run(timestamp=self.timestamp+timedelta(days=30))
```

Add new Tasks to Job-Queue
--------------------------

Party exposes a HTTP interface which can be used to interact with the Job Queue.

A task input must be of the following format:
```python
{
    'party': <String> Required //Name of the TaskHandler
    'data': <Dictionary> Optional //Additional Data for Task
    'timestamp': <datetime object> Optional //Timestamp when this task should be executed, system_now() by default
}
```

Since the timestamp field is optional, the default behavior is to queue the Task for immediate execution in the next available slot.

```python
from simtools.uhttpclient import BlockingHTTPClient

con = BlockingHTTPClient("/tmp/party.sock") #If listening on a Unix Domain Socket.
con = BlockingHTTPClient("127.0.0.1:8032") #If listening on a TCP Port.
```

You can add a new task to the jobQueue using a POST request.

```python
data = {"tasks": [
  {
    "party": "SampleTask",
    "data": {"arg1": "val1"},
    "timestamp": datetime.datetime(2014, 03, 11, 05, 12, 22, 86400)
  },
]}

con("/tasks", None, data=data, method="POST")
```

Tasks accepts a list, so you can add multiple tasks in the same POST request.

```python
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
```
The request returns a list of all the tasks that were added to the Queue.

Task Execution
---------------

Based on the number of workers available, job scheduler fetches new tasks from the Queue and dispatches them for Execution.

Tasks with a lower timestamp are executed ahead of others. Scheudler is biased towards freshness and success, so if two tasks have the same timestamp one with lower attempts is preferred. When all other paramaters match, One that was creared earlier, is preferred.

Rules

* Task once Completed cannot be reexecuted.
* Tasks once Deleted, cannot be executed.
* Task cannot be executed after 6 failed attempts.

The execution process is automatic and is handled by a Job scheduler which can be started using a simple command

```python
python scheduler/
```

However, for the impatient developer there is a backdoor execution available. A POST request on /task will immediately execute the Task, irrespective of the Queue Status.

Do note that this is a blocking operation and should not be used in Production system as it can block the available processors on Party HTTP interface, which inturn can choke the inflow of tasks.

Sample Usage:
```python
con("/task/{TASK_ID}", None, method="POST")
```

Database Connections
--------------------

Using pymongo.MongoClient() directly in party is not advised.
Instead trust kernel.dbapi for all your needs.

Advantages of doing this over MongoClient

1.  kernel.dbapi takes care of all production system scenarios where
    different databases run on different hosts. Party has access to all
    the pre decided Hosts and Ports. Putting such deployment critical variables
    in your application specific logic would be a nightamare to maintain.
2.  kernel.dbapi auto maintains optimum connection pooling.
3.  kernel.dbapi handles MongoDB sharding and allows you to connect to Slaves instead of Master for less important tasks.
4.  Any update in the core logic does not have to be re-implemented in your application.


Examples

Using Collection

```python
    from kernel.pool import dbapi

    user_collection = dbapi(dbapi.prod, "user")
    assets_collection = dbapi(dbapi.blackjack, "asset")

    assets_collection.find( ...  use whatever logic ... )
```

If you just want access to the database:

```python
    db = dbapi.get(dbapi.blackjack)

    OR
    
    db = dbapi.get(dbapi.prod)
```
