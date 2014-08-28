---
layout: page
title: "Templating Basics"
description: "Add a view to Siminars. Add a url, and an api call"
category: tut
date:  2014-08-29 19:05:07
tags: []
---

### Introduction
We have seen till now how to add simple Hello world page to siminars. This is an attempt to take this knowledge further and make something like the final output without the javascript framework added. Following are the code changes that can get something visually similar to the final output.




### Server Side

```python
class HelloHandler(BaseHandler):
    @tornado.web.authenticated
    def get(self, tabname):
        template = "hello_world/hello"
        context = {}
        context['tabname'] = tabname

        self.render_to_response(template, context)
```

As you can see in server side we have added a tabname context and argument. The tabname argument comes from the url from the url pattern as explained earlier. The group name is an essential part of the regex pattern in siminars Url dispatch mechanism.

```python

Url(r"/hello/(?P<tabname>\w+)/?$",
        valid_urls_handlers.HelloHandler, "hello"),

```


For context we have added the tabname in the context of the template. One arrives at this function only after login because of the authenticated decorator.


### Template

```html
<?py _context['_layout'] = 'base.t_html' ?>


<?py with capture_as('body'): ?>
<div class="container">
  <div class="svtermsofuse"> <!-- content container -->

    <br/>
    <br/>
    <br/>
    <br/>

    <div id="container">
      <div class="nav nav-tabs">

        <div class="smstepaddcontrol">
            <?py view_class = 'btn-warning' if tabname=='hello' else 'btn-primary' ?>
            <a href="#{this.reverse_url('hello', tabname='hello')}" class="btn ${view_class}" rel="hello">Hello</a>
            <?py view_class = 'btn-warning' if tabname=='tabbed' else 'btn-primary' ?>
            <a href="#{this.reverse_url('hello', tabname='tabbed')}" class="btn ${view_class}" rel="tabbed">Tabbed</a>
            <?py view_class = 'btn-warning' if tabname=='world' else 'btn-primary' ?>
            <a href="#{this.reverse_url('hello', tabname='world')}" class="btn ${view_class}" rel="world">World</a>
            <?py view_class = 'btn-warning' if tabname=='collate' else 'btn-primary'?>
            <a href="#{this.reverse_url('hello', tabname='collate')}" class="btn ${view_class}" rel="collate">Collate</a>
          </div>

      </div>
    </div>

  </div>
</div> <!-- end content container -->

<?py #endwith ?>

```

### Explanation


```text
<?py _context['_layout'] = 'base.t_html' ?>
```

This extends the layout of the page from base. If you notice many components of the final page have not been explicitly written in html. These are extended from the base template.

```text
<?py with capture_as('body'): ?>
```
This tells the templating engine that we are customizing the body part of the template.

There are many such default constructs that base template has.

```html
<?py if not captured_as('css'): ?>

<?py #endif ?>
<title>
<?py if not captured_as('title'): ?>
  Siminars
<?py #endif ?>
</title>
<?py if not captured_as('js'): ?>

<?py #endif ?>


```

These are the definition on the template sides. Hence as long as we do not customize css, title or js with a <?py with capture_as(js): ?> for example a default for that block will automatically added 

```html
<?py view_class = 'btn-warning' if tabname=='hello' else 'btn-primary' ?>
```

run python code inside the tenjin templating engine.

```html
${view_class}
```
Showing context variables in the template.


### Output

![Final Output](/img/complete.png?raw=true)
