#Deployment Servers & Services

___

## ProdDb
Runs Production & Blakjack Database

### mongoDb
    Port 27017
    Version 2.6 & above
___

## SubpubDB

### mongoDb
    Port 27017
    Version 2.6 & above

---

## ReportingDB

### Postgres

    Port {default}
    Version 9.0 & above
    dbname serverlogs
    
Users
    
    log_feeder (write only access to bjdump/apidump)
    log_viewer (read only access)
    cable_guy (root)
    
___

## Party

### mongoDb
    Port 27017
    Version 2.6 & above

### agora_party
    url: git@github.com:Simversity/agora_party branch: prod_party

Dependencies:

    psycopg2
    simtools (prod_simtools from git@github.com:Simversity/simtools)

local_settings.py

    DEBUG=False
    DB_HOST = "{proddbHost}"
    
    EMAIL_DUMMY = False

    BLACKJACK_HOST = 'siminars.com'
    USHER_HOST = "{applicationServerHost}:9011"
    ELASTIC_HOST = "http://{elasticSearchHost}:9200"
    APIHOSTS = '{applicationServerHost}:8001'

    ERROR_TO = "dev@siminars.com"
    BILLING_NOTIFY = "payments@siminars.com"

    SHORTNER_MASK = "http://smnr.me"
    SHORTNER_HOST = "10.0.0.22"
    SHORTNER_DUMMY = False

    #SUPPRESS_EMAIL = True
    SENDER_NAME = "Siminars"
    LOG_DB_HOST = "serverlogs.simversity.com"
    #SKIP_TASKS = ["BJLogs", "APILogs", "RouterLogs"]

    TASKS_TO_CONSIDER = ["PendingActivity", "UpcomingActivity"]
    SEND_PENDING_DAY = 5
    #TASKS_TO_CONSIDER = ["UpcomingActivity"]

    #PayPal Live
    PAYPAL_USERNAME = 'payments_api1.simversity.com'
    PAYPAL_PASSWORD = '86LQL87P2QB6T9TP'
    PAYPAL_SIGNATURE =    'AFcWxV21C7fd0v3bYYYRCpSSRl31A2ijbffnRZxlY127F1C26HCmEtjz'
    PAYPAL_VERSION = "82.0"
    
    
### agora_party2
Reuse the party package

local_settings.py

    DEBUG = False
    
    UNIX_SOCKET = /tmp/party2.sock

### Job Scheduler1
Reuse the party package

local_settings.py

    TASKS_TO_SKIP = ["PendingActivity", "UpcomingActivity"]
    WORKERS = [no. of cores]
    WORKER_INTERVAL = 2

### Job Scheduler2

Reuse the party package

local_settings.py

    DEBUG = False
    
    TASKS_TO_CONSIDER = ["PendingActivity", "UpcomingActivity"]
    WORKERS = [no. of cores / 2]
    WORKER_INTERVAL = 5

___

## ElasticSearch

### elasticsearch

    Port 9200
    Version 1.0 & above

---

## Application Server

### API1
Code

    agora_api: git@github.com:Simversity/agora_api branch: prod_api
    simtools: git@github.com:Simversity/simtools branch: prod_simtools
    
local_settigs.py

    DEBUG = False
    
    LOGGER = "buffer"
    
    UNIX_SOCKET = /tmp/api1.sock
    DB_HOST = {prodDbHost}
    DB_PORT = {prodDbPort}
    
    PARTYHOST = "{party_host}:8032"
    SUBPUB_HOST = "127.0.0.1:{nginx_subpub_port}"

### API2
Code

    {same as API1}
    
local_settigs.py (Same as API1. With the following changes).

    UNIX_SOCKET = /tmp/api2.sock
    
### Blackjack1
Code

    blackjack: git@github.com:Simversity/blackjack branch: prod_blackajck
    simtools: git@github.com:Simversity/simtools branch: prod_simtools
    
local_settigs.py

    DEBUG = False
       
    LOGGER = "buffer"
    SITESEARCH = False
    
    USE_CUSTOM_FONT = True
    FRONT_END_ERROR = True
    
    DOMAIN = "siminars.com"
    
    ALLOW_ANALYTICS = True
    USE_CACHE = True

    USE_COMPRESSED_JS = True
    USE_COMPRESSED_CSS = True
    USE_GZIP = True
    
    TMPL_PATH = "{path_to_blackjack_checkout}/templates"
    
    UNIX_SOCKET = /tmp/blackjack1.sock
    
    APIHOSTS = "127.0.0.1:{apiport}"
    PARTYHOST = "{partyhost:8032}"
    
    STATIC_URL = "http://d3z60wkw2l5fo.cloudfront.net/static/"
    DROPBOX_URL = "http://d3z60wkw2l5fo.cloudfront.net/static/"
    
    REPORT_USER = "log_viewer"
    REPORT_PASSWORD = "l0gv13w3r"
    REPORT_DBNAME = "serverlogs"
    REPORT_HOST = "{ reporting_db_host }"
    
### Blackjack2
Code

    Same as Blackjack1
    
local_settings.py (Same as Blackjack1 with the following changes)
    
    UNIX_SOCKET = "/tmp/blackjack2.sock"