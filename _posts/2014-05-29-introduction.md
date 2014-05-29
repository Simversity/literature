---
layout: page
title: "Introduction"
category: api
date: 2014-05-29 19:39:46
order: 1
---

API is the primary interface to Siminars' Data.

It is mostly RESTful, barring a few old requests, and most resources can be accessed using GET, PUT, POST & DELETE requests.

#### API Connection

Through out this documentation we will assume that you have an active connection to the API Server.
You can create a new connection using various types of HTTPClients exposed by simtools.uhttpclient.

For simplicity's sake we will use BlockingHTTPClient in this example.

	from simtools.uhttpclient import BlockingHTTPClient

	con = BlockingHTTPClient("/tmp/api.sock")
	#If listening on a Unix Domain Socket.

	con = BlockingHTTPClient("127.0.0.1:{PORT}")
	#If listening on a TCP Port of your choice. Default is 8000.

#### Content-Type
The API uses HTTP calls for all communication and only accepts Content-Type: application/javascript as a valid content-type.

#### Apitoken
All API requests require a valid apitoken. In DEBUG mode you can use apitoken=2 to accesss all the data.


#### Data Return Format
API's return is always a JSON dict of the format

	{
		"code": <int>,
		"data": <dict>,
		"error": <HTTPErrorinstance>, //Optional
		"message": <string> //Optional
	}

Code is the HTTP Status Code for the corresponding action.

In case of and Error data would always be None and message would contain the reason of the failure.
In case everything is ok and there is no Error, both error & message would be None.

##### Status Codes

Status codes used with the corresponding Integer value.

- OK = PASS = 200
- CREATED = 201
- ACCEPTED = 202
- RESET = 205
- SEE_OTHER = 303
- LOGIN_REQUIRED = 401
- PAYMENT_REQUIRED = 402
- ACCESS_DENIED = 403
- NOT_FOUND = 404
- NOT_ALLOWED = 405
- CONFLICT = 409
- NULL_PARAM = 412
- CONDITION_FAIL = 412
- INVALID_PARAM = 412
- FAIL = 500
- INTERNAL_ERROR = 500