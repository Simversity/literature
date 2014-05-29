---
layout: page
title: "Introduction"
category: party
date: 2014-05-29 19:55:05
order: 1
---

#### Introduction

Party is an application framework designed to delegate work to other processes. It works on a post-fork model and spawns a group of workers, 1 per CPU core. It is used in a variety of applications and is one of the core components of our distributed processing architecture.

#### Use-Cases
Party can be used for a wide variety of tasks that are either Cron based, need to be processed on a future timestamp, need to perform CPU intensive work, require Batch/Data processing, or perform overnight Map Reduce on the production database.

Examples

1. Send Email Notifications.
2. Processing Search Database updates.
3. Churn Forum & Feed based on Edge Rank algorithm.
4. Sending Weekly reminders.
5. Computing monthly Bills.
6. Encoding Videos.
7. Delete Orprhan data from the NoSQL database.
8. Update denormalized data periodically.
9. Schedule migrations to Reporting Database.