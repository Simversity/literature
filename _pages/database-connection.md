---
layout: page
title: "DB Connection"
category: party
date: 2014-05-29 19:56:56
order: 8
---

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

    from kernel.pool import dbapi

    user_collection = dbapi(dbapi.prod, "user")
    assets_collection = dbapi(dbapi.blackjack, "asset")

    assets_collection.find( ...  use whatever logic ... )


If you just want access to the database:

    db = dbapi.get(dbapi.blackjack)
    OR
    db = dbapi.get(dbapi.prod)