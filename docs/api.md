Introduction
------------

API is the primary interface to Siminars' Data.

It is mostly RESTful, barring a few old requests, and most resources can be accessed using GET, PUT, POST & DELETE requests.
The API uses HTTP calls for all communication and only accepts Content-Type: application/javascript as a valid content-type.

All API requests require a valid apitoken. Most API requests would require a valid authtoken, unless stated otherwise. Generation of both these tokens shall be discussed separately.
