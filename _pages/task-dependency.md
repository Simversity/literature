---
layout: page
title: "Task Dependency"
category: party
date: 2014-05-29 19:56:26
order: 5
---

Party is designed to run in parallel but there are situations where tasks have to be executed sequentially. This can be achieved but must be avoided as far as feasible, as it breaks the overall design. scheduler/task_base.py exposes a decorator depends_on which accepts a list of tasks that must be executed ahead of the current task. If the dependencies are not met (Dependency Tasks not found in the Queue or they are scheduled for later) the execution is delayed by 10 minutes. Do note that this will continue to be delayed unless all dependencies are met. To save resources you must try fix the problems as early as possible.

Example:

	from scheduler.task_base import TaskBase, depends_on

	class AssetEncoding(TaskBase)
	    @depends_on(["AssetsToCache"])
	    def run(self):
	        self.update_transcoded_jobs()