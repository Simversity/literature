---
layout: page
title: "Adding urls and backend handler for a new page/extension."
description: "Adding urls and backend handler for a new page/extension."
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
from handlers import hello as hello_handler
 
urlpatterns += [
    Url(r"/hello/(?P<tabname>\w+)/?$",
        hello_handler.HelloHandler, 'hello'),
]

``` 

The first argument here is pattern, second handler class in blackjack and the third url name.

An important thing to note and a difference from urldispatch of django is that regex pattern always should be a named group. This is because this is used in the reverse url mapping mechanism of Urls.

```python

simple_url = lambda s: rurl.subn(
    lambda x: "%%(%s)s" % x.groups()[1], sd(sc(s)))[0]

``` 



### Writing a blackjack Handler

To add a view handler to blackjack. Add it to handlers folder in blackjack. In case we want to add a new handler. Add a file for the handler and a view class for it in the following manner.

```python
blackjack/handlers/hello.py

from core import BaseHandler

class HelloHandler(BaseHandler):


    def get(self, tabname):
    	template = "hello/hello"
       	context['text'] = "Hello World"

        self.render_to_response(template, context)


```

As you can see HelloHandler extends BaseHandler. BaseHandler is itself an extension of [tornado web request handlers](http://www.tornadoweb.org/en/branch2.1/web.html). Which providers helper api for http requests. Apart from that BaseHandler provides additional tools and api for handling http requests. Providing a layer for cross platform optimization and tools etc... , request context handling, context handling and api's for templating.


 

Tornado is a non blocking server that provides an extensive api for asynchronous code.



```python
def get(self, tabname):
```
In example urlpattern r"/hello/(?P<tabname>\w+)/?$" , the second part is the argument for this function.







```python

template = "hello/hello"
context = {}
context['text'] = "Hello World"

self.render_to_response(template, context)
```

This(self.render_to_response) is helper function from  BaseHandler which renders the template with the given context. If you don't pass them like this. It will use self.template and self.context.

### Adding authentication and other decorators
What do you when you have to make sure the url is only accessible to an authenticated user. Enter helper decorators.

from core import BaseHandler
import core

```python
class HelloHandler(BaseHandler):

    @tornado.web.authenticated
    @core.decorators.signature(params=['someparam'])
    def get(self, tabname):
    	template = "hello/hello"
       	context['text'] = "Hello World"

        self.render_to_response(template, context)

```

This Handler has some helper [decorators](http://simeonfranklin.com/blog/2012/jul/1/python-decorators-in-12-steps/).

Lets see what these decorators do:

```python
@tornado.web.authenticated
@core.decorators.signature(params=['someparam'])
def get(self, tabname, someparam)
```

tornado.web.authenticated checks whether or not the user is authenticated. If not it redirects the user to a login url specified.

The core.decorators.signature validates the request if it has all the params or not for example here it will check whether the url contain the get parameter someparam.Otherwise the server would complain

```text

HTTPError: HTTP 412: Precondition Failed (someparam is a required parameter)
  
```


### Basic Template

<?py _context['_layout'] = None ?>
<html>
<body>

${text}

</body>

</html>

```text

${text} -> shows the context shared to the template

```

```text

<?py _context['_layout'] = None ?>

```

By default the template loads a layout from the base template this basically tells the templating engine not to do that. We will dive into templates in the next section

### Output

![Complete](/img/helloworld.png?raw=true)

