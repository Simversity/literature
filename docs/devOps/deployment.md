#Deployment Servers & Services

___

## ProdDb
Runs Production & Blakjack Database

### mongoDb
    Port Deafult <default>
    Version 2.6 & above
___

## SubpubDB

### mongoDb
    Port Default <27017>
    Version 2.6 & above



## ReportingDB

### Postgres

    Port Default <default>
    Version 9.0 & above
    dbname serverlogs
    
Users

    cable_guy (root, owns everything)
    log_feeder (write-only access to bjdump/apidump)
    log_viewer (read-only access to entire database)
    aws_feeder (write-only access to prod_db dump)
    
___

## Party

### Nginx
Version
    
    any

nginx.conf

    user ubuntu;
    worker_processes  {no. of cpu}*2;

    error_log  /tmp/nginx/error.log;
    pid        /tmp/nginx/nginx.pid;

    events {
        worker_connections 18000;
        use epoll;
    }

    http {
        upstream party {
            server unix:/tmp/party.sock;
            server unix:/tmp/party2.sock;
        }

        include       mime.types;
        default_type  text/plain;
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;

        gzip on;
        gzip_comp_level 6;
        gzip_types text/plain
            text/css
            text/xml
            application/x-javascript
            application/xml
            application/atom+xml
            application/javascript
            text/javascript
            application/json;

        gzip_proxied any;
        gzip_disable "msie6";

        proxy_next_upstream error timeout http_502;

        server {
            listen 8032;

            location / {
                access_log /tmp/nginx/party_access.log;
                proxy_pass_header Server;
                proxy_set_header Host $http_host;
                proxy_redirect off;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_pass http://party;
                proxy_http_version 1.1;
                proxy_set_header Connection "";
            }
        }
    }

### mongoDb
    Port Default <27017>
    Version 2.6 & above

### agora_party

Code:

    url: git@github.com:Simversity/agora_party branch: prod_party

Dependencies:

    psycopg2
    simtools (prod_simtools from git@github.com:Simversity/simtools)

local_settings.py

    DEBUG = False
    DB_HOST = {proddbHost}
    EMAIL_DUMMY = False

    BLACKJACK_HOST = {domain_name} eg. siminars.com / staging.simianrs.com
    USHER_HOST = {applicationServerHost}:9011
    ELASTIC_HOST = http://{elasticSearchHost}:9200
    APIHOSTS = {applicationServerHost}:8001

    SHORTNER_DUMMY = False
    SHORTNER_MASK = {shortener_domain_to_use} eg. http://smnr.com
    SHORTNER_HOST = {shortener_host}

    LOG_DB_HOST = {reporting_db_host}
    
    
### agora_party2
Code

    Reuse the party package

local_settings.py

    (same as party1 except the following changes)
    UNIX_SOCKET = /tmp/party2.sock

### Job Scheduler1
Reuse the party package

local_settings.py

    TASKS_TO_SKIP = ["PendingActivity", "UpcomingActivity"]
    WORKERS = {no. of cores}
    WORKER_INTERVAL = 2

### Job Scheduler2

Reuse the party package

local_settings.py

    DEBUG = False
    
    TASKS_TO_CONSIDER = ["PendingActivity", "UpcomingActivity"]
    WORKERS = {no. of cores}/2
    WORKER_INTERVAL = 5

___

## ElasticSearch

### elasticsearch

    Port 9200
    Version 1.0 & above

---

## Application Server

###Nginx
Nginx needs nginx_upload_module to be compiled & installed from source.

Recipe

    http://developers.siminars.com/devOps/nginx/

nginx.conf

    user ubuntu;
    worker_processes  {no. of cpu}*2;

    error_log  /tmp/nginx/error.log;
    pid        /tmp/nginx/nginx.pid;

    events {
        worker_connections 18000;
        use epoll;
    }

    http {
        upstream blackjack {
            server unix:/tmp/blackjack1.sock;
            server unix:/tmp/blackjack2.sock;
        }

        upstream api {
            server unix:/tmp/api.sock;
            server unix:/tmp/api2.sock;
        }

        upstream subpub {
            server unix:/tmp/subpub.sock;
            server unix:/tmp/subpub2.sock;
        }

        include       mime.types;
        default_type  text/plain;
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;

        gzip on;
        gzip_comp_level 6;
        gzip_types text/plain
            text/css
            text/xml
            application/x-javascript
            application/xml
            application/atom+xml
            application/javascript
            text/javascript
            application/json;

        gzip_proxied any;
        gzip_disable "msie6";

        proxy_next_upstream error timeout http_502;

        server {
            listen 8001;

            location / {
                access_log /tmp/nginx/api_access.log;
                proxy_pass_header Server;
                proxy_set_header Host $http_host;
                proxy_redirect off;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_pass http://api;
                proxy_http_version 1.1;
                proxy_set_header Connection "";   
            }
        }

        server {
            listen 9011;

            location / {
                access_log /tmp/nginx/subpub_access.log;
                proxy_pass_header Server;
                proxy_set_header Host $http_host;
                proxy_redirect off;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_pass http://subpub;
                proxy_http_version 1.1;
                proxy_set_header Connection "";   
            }
        }

        server {
            listen           443;
            server_name      siminars.com;
            ssl              on;
            ssl_certificate  certificates/siminars.com/siminars.com.bundle.crt;
            ssl_certificate_key  certificates/siminars.com/siminars.com.key;
            ssl_protocols SSLv3 TLSv1;
            ssl_ciphers ALL:!aNULL:!ADH:!eNULL:!LOW:!EXP:RC4+RSA:+HIGH:+MEDIUM;

            location ~ ^/(billing|payment|x/proxy|x/party|modalstore|urlsstore|render) {
                access_log  /tmp/nginx/ssl_access.log;
                proxy_pass_header Server;
                proxy_set_header Host $http_host;
                proxy_redirect off;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_pass http://blackjack;
                proxy_http_version 1.1;
                proxy_set_header Connection "";   
            }

            location / {
                rewrite     ^(.*)   http://siminars.com$request_uri?;
            }
        }

        server {
            client_max_body_size 200M;

            error_page 500 502 503 504 /502.html;

            location /502.html {
              root /home/ubuntu/workspace/blackjack/templates/raw/;
              allow all;
            }

            location /x/upload {
                upload_pass   /internalupload;
                upload_pass_args on;
                upload_store /tmp/nginx/uploads 1;
                upload_state_store /tmp/nginx/uploads 1;
                upload_max_file_size 200M;
                upload_pass_form_field "^.+$";
                upload_set_form_field $upload_field_name.name "$upload_file_name";
                upload_set_form_field $upload_field_name.content_type "$upload_content_type";
                upload_set_form_field $upload_field_name.path "$upload_tmp_path";
                upload_aggregate_form_field "$upload_field_name.md5" "$upload_file_md5";
                upload_aggregate_form_field "$upload_field_name.size" "$upload_file_size";
                upload_cleanup 400 404 499 500-505;
            }

            location /internalupload {
                proxy_pass_header Server;
                proxy_set_header Host $http_host;
                proxy_set_header Keep-Alive timeout=1;
                proxy_redirect off;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_pass http://blackjack/nginx-upload;
                proxy_read_timeout 15m;
                proxy_http_version 1.1;
                proxy_set_header Connection "";   
            }

            location /google93e78432f77d32fa.html {
                alias /home/ubuntu/verifications/google93e78432f77d32fa.html;
            }

            location /robots.txt {
                alias /home/ubuntu/workspace/blackjack/media/robots.txt;
            }

            location ~ ^/(billing|payment) {
                rewrite     ^(.*)   https://siminars.com$request_uri?;
            }

            location / {
                access_log  /tmp/nginx/access.log;
                proxy_pass_header Server;
                proxy_set_header Host $http_host;
                proxy_redirect off;
                proxy_set_header X-Real-Ip $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_pass http://blackjack;     
                proxy_http_version 1.1;
                proxy_set_header Connection "";   
            }
        }
    }

### API1
Code

    agora_api: git@github.com:Simversity/agora_api branch: prod_api
    simtools: git@github.com:Simversity/simtools branch: prod_simtools
    
local_settigs.py

    DEBUG = False
    
    LOGGER = "buffer"
    
    UNIX_SOCKET = /tmp/api1.sock
        
    DB_HOST = {prodDbHost}
    PARTYHOST = "{party_host}:8032"
    SUBPUB_HOST = "127.0.0.1:{nginx_subpub_port}"

### API2
Code

    same as API1
    
local_settigs.py

    (Same as API1. With the following changes)
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
    
    DOMAIN = {domain_name}
    
    USE_CACHE = True
    ALLOW_ANALYTICS = True

    USE_GZIP = True
    USE_COMPRESSED_JS = True
    USE_COMPRESSED_CSS = True
    
    TMPL_PATH = "{path_to_blackjack_checkout}/templates"
    
    UNIX_SOCKET = /tmp/blackjack1.sock
    
    APIHOSTS = "127.0.0.1:{apiport}"
    PARTYHOST = "{partyhost}:8032}"
    
    VERSION = {static_content_version}
    
    STATIC_URL = "http://d3z60wkw2l5fo.cloudfront.net/static/"
    DROPBOX_URL = "http://d3z60wkw2l5fo.cloudfront.net/static/"
    
    REPORT_USER = "log_viewer"
    REPORT_PASSWORD = "l0gv13w3r"
    REPORT_DBNAME = "serverlogs"
    REPORT_HOST = "{reporting_db_host}"
    
### Blackjack2
Code

    Same as Blackjack1
    
local_settings.py

    (Same as Blackjack1 with the following changes)
    UNIX_SOCKET = "/tmp/blackjack2.sock"
    
### subpub_tcp1
Code

    usher: git@github.com:Simversity/usher branch: prod_usher

Dependencies
    
    golang
    wget https://godeb.s3.amazonaws.com/godeb-amd64.tar.gz
    $ ./godeb install
    
    export GOPATH={path_to_usher_checkout}
    export PATH=$GOPATH/bin:$PATH
    
    easy_install bzr
    go get labix.org/v2/mgo
    
    go get code.google.com/p/gcfg
    go get github.com/garyburd/redigo/redis
    
    go install subpub_tcp
    go install standalone

subpub_settings.cfg
    
    [subpub]
    SenderName={domain_name}
    ProdHost="127.0.0.1:27019"
    SubPubHost="127.0.0.1:27019"
    BlackjackHost="127.0.0.1:27019"
    

Usage

    subpub_tcp -config={path_to_subpub_settings.cfg} -UNIX_SOCKET=/tmp/subpub1.sock
    
### subpub_tcp2
Code

    (same as subpub_tcp1)
    
Usage

    subpub_tcp -config={path_to_subpub_settings.cfg} -UNIX_SOCKET=/tmp/subpub2.sock
