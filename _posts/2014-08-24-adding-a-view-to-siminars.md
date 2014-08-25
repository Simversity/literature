---
layout: page
title: "Adding a view to siminars"
description: "Add a view to Siminars. Add a url, and an api call"
category: tut
date: 2014-08-29 19:05:08
tags: []
---

### Adding a view

Adding a view entails adding a url in blackjack for the particular view. The api for Siminars framework's urldispatch is very similar to urldispatch of django.

To add a url. Create a Url object and extend the urlpatterns

Url object takes pattern, handler_class and named url pattern for reverse resolution as required arguments.

```python

from simtools.server_utils import Url
from handlers import validurls as valid_urls_handler
 
urlpatterns += [
    Url(r"/valid/(?P<user_id>\d{19}63702)/?$",
        valid_urls_handlers.ValidUrls, 'valid_urls'),
]

``` 

The first argument here is pattern, second handler class in blackjack and the third url name.

An important thing to note and a difference from urldispatch of django is that regex pattern always should be a named group. This is because this is used in the reverse url mapping mechanism of Urls.

```python

simple_url = lambda s: rurl.subn(
    lambda x: "%%(%s)s" % x.groups()[1], sd(sc(s)))[0]

``` 



### Writing a blackjack Handler

To add a view handler to blackjack. Add it to handlers folder in blackjack. In case we want to add a new handler. Add a file for the handler and a view Class for it in the following manner.

```python
blackjack/handlers/validurls.py

import tornado
import tornado.gen as gen
from core import BaseHandler

class ValidUrls(BaseHandler):
    template = "validurls/validurls"

    @tornado.web.authenticated
    @tornado.web.asynchronous
    @tornado.gen.engine
    def get(self, user_id):
        ret, status = yield gen.Task(
            self.api.caller.get,
            self.api.caller.get_url('urls')
        )
        urls = ret.get("urls", {})
        
        self.context['user_id'] = str(user_id)
        self.context['urls'] = urls
        
        self.render_to_response()


```

As you can see ValidUrls extends BaseHandler. BaseHandler is itself an extension of [tornado web request handlers](http://www.tornadoweb.org/en/branch2.1/web.html). Which providers helper api for http requests. Apart from that BaseHandler provides additional tools and api for handling http requests. Providing a layer for cross platform optimization and tools etc... , request context handling, context handling and api's for templating. 

Tornado is a non blocking server that provides an extensive api for asynchronous code.

```python
@tornado.web.authenticated
@tornado.web.asynchronous
@tornado.gen.engine
```
This is an example of some of the decorators one might use for creating a view. The first one here checks if the user is authenticated. If not the user is redirected to login url. 
The web.asynchronous decorator does not make a method asynchronous; it tells the framework that the method is asynchronous.
gen.engine, Decorator for asynchronous generators. 



```python
def get(self, user_id):
```
In example urlpattern r"/valid/(?P<user_id>\d{19}63702)/?$ , the second part is the argument for this function.


```python
ret, status = yield gen.Task(
            self.api.caller.get,
            self.api.caller.get_url('urls')
        )
urls = ret.get("urls", {})
```

Read tornado's [gen.task](http://tornado.readthedocs.org/en/latest/gen.html#tornado.gen.Task)



```python

template = "validurls/validurls"


self.context['user_id'] = str(user_id)
self.context['urls'] = urls
self.render_to_response()
```

This is helper function from  BaseHandler which renders the template with the given context(self.context)
 