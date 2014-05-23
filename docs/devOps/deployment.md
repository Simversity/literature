#Deployment Servers & Services

## ProdDb
Runs Production & Blakjack Database

### mongoDb
 - Port 27017
 - Version 2.6 & above

## SubpubDB

### mongoDb
 - Port 27017
 - Version 2.6 & above

## Party

### mongoDb
 - Port 27017
 - Version 2.6 & above

### agora_party
 - prod_party from git@github.com:Simversity/agora_party

Dependencies:

 - psycopg2
 - simtools (prod_simtools from git@github.com:Simversity/simtools)

local_settings.py:
 - UNIX_SOCKET=/tmp/party1.sock

### agora_party2
Reuse the party package

local_settings.py

 - UNIX_SOCKET=/tmp/party2.sock

## Scheduler1
Reuse the party package

local_settings.py

 - TASKS_TO_SKIP = ["PendingActivity", "UpcomingActivity"]
 - WORKERS = [no. of cores]
 - WORKER_INTERVAL = 2

## Scheduler2

Reuse the party package

local_settings.py

 - TASKS_TO_CONSIDER = ["PendingActivity", "UpcomingActivity"]
 - WORKERS = [no. of cores / 2]
 - WORKER_INTERVAL = 5
