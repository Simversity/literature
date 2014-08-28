---
layout: page
title: "Keeping states using routes"
description: "Keeping states with routes"
category: tut

---

### Introduction

Now we could get our required result with the last example but there are some
things missing. For one, every request refreshes the whole page. That is
undesirable for natural usage for the user. And the page is repainted by the
browser again and again.

Enter routes. Routes is a siminar library which uses history specs in html5.
routes provides a mechanism to re-play the actions based on the URL state.

In a traditional approach a developer traps a DOM event and triggers a
corresponding UI action with no measure to redo the changes on page refresh or
open-in-new-tab.

_route solves this problem by changing the click-based events to URL based
events.

The application developer registers listeners for url_patterns, absolute_urls OR
get_arguments, and any change in the value would trigger the registered
listener. So a click would _ONLY_ push a state and _route will handle the rest.

_route ALSO solves the problem of replaying the triggers as a user goes back &
forward through the browser history.

Example:

Our single page application is accessed by the URL "/hello/<tab_name>/" where
tab_name could be "hello", "tabbed" or "world"

Step1 is to register listeners of this URL.

```
_route.unbind_bind(";hello", function(){ console.log("Tab changed"); })
```

Step2: Bind all clicks to push the destination URL.

```
$("#tab1").unbind("click").bind("click", function() {
  var elem = $(this);
  _route.push(elem.attr('href'));
});
```

Flow:

1.  Viewer clicks the button.

2.  JS executes _route.push(url)

3.  _route checks for available listeners that match <url>.

4.  If any listeners are available, the url is pushed to the history_stack & the
    navigation bar and the corresponding listener is triggered.



## Routes



```javascript
    function () {
        var self = this;
        function tab_change_listener(kw) {
           
            $(".text_here").text("The tab clicked is " + kw.url_args['tabname']);
            $('a', self.cache_l.c).removeClass("btn-warning");
            $('a[rel='+ kw.url_args.tabname +']', self.cache_l.c).addClass("btn-warning");

        }

        _route.unbind_bind(';hello', tab_change_listener, function() {
            console.log("All bindings have been completed");
            _route.refresh();
        });
    },

```

Lets see what the _route in this snippet is all about

```javascript
	_route.unbind_bind(';hello', tab_change_listener, function() {
            console.log("Awesome change");
            _route.refresh();
        });
```

This snippet binds a certain function tab_change_listener. This basically means that whenever the url state changes to hello/<something>, the tab_change_listener function is triggered. Hence we can do the actions for tab change here. You can pass a callback at the end of unbind_bind. In this case the callback actually runs _route.refresh which emulates a url refresh of the page.
  

### Implementing our UI with routes

Server side:

Before you add a js file to siminars. You have to tell the asset management system about it. The way you do that is by adding the file in blackjack/utils/js_map.py

 
```python
DEBUG_JS = { ...

    "hello":[
        "jack_js/hello.js",
        ],
}
```






Template changes:

On the template side we add this snippet in our template

```html
<?py with capture_as('js'):?>
#{ the.load_js_dependencies(this, "hello") }
<script type="text/javascript">
  $(function() {
      var _hello = new Hello("${tabname}");
      _hello.init()
      });
</script>
<?py #endwith ?>
```

This tells template system we are customizing the js part of the base layout and 

```text
#{ the.load_js_dependencies(this, "hello") }
```

This loads the js files defined in js_map under the key hello.


```html
<?py _context['_layout'] = 'base.t_html' ?>

<?py with capture_as('js'):?>
#{ the.load_js_dependencies(this, "valid") }
<script type="text/javascript">
  $(function() {
      var _hello = new Hello();
      _hello.init()
      });
</script>
<?py #endwith ?>

<div class="svtermsofuse"> <!-- content container -->

    <br/>
    <br/>
    <br/>
    <br/>

    <div id="container">
    <div class="nav nav-tabs">

        <div class="smstepaddcontrol">
            <a href="#{this.reverse_url('hello', tabname='hello')}" class="btn btn-primary" rel="hello">Hello</a>
            <a href="#{this.reverse_url('hello', tabname='tabbed')}" class="btn btn-primary" rel="tabbed">Tabbed</a>
            <a href="#{this.reverse_url('hello', tabname='world')}" class="btn btn-primary" rel="world">World</a>
            <a href="#{this.reverse_url('hello', tabname='collate')}" class="btn btn-primary" rel="collate">Collate</a>
          </div>

    </div>
    </div>

    <span class="text_here"></span>


    </div>
</div> <!-- end content container -->
```
Since we are doing this in js now we can simplify the template  bit.

### Javascript Changes:

```javascript
var Hello = function (tabname) {
    var self = this;
    self.tabname = tabname;
    self.init();
};

Hello.prototype = {
    cache_l: {},
    
    init:  function () {
	var self = this
	self.urls = {}
	self.urls = {'hello':'/hello/hello',
		     'tabbed':'/hello/tabbed',
		     'world':'/hello/world',
		     'collate':'/hello/collate'
		    }
	self.cache_l.c = $('#container');
	self.cache_l.links = self.cache_l.c.find("a.btn-primary");
	self.bind_route();
	self.bind();
	
	
    },
    
    
    
    bind_route: function () {
	var self = this;
	function tab_change_listener(kw) {
	    if (kw.url_args['tabname'] == 'collate'){
		self.get_data()
	    }
	    else{
		$(".text_here").text("The tab clicked is " + kw.url_args['tabname']);
	    }
	    $('a', self.cache_l.c).removeClass("btn-warning");
	    $('a[rel='+ kw.url_args.tabname +']', self.cache_l.c).addClass("btn-warning");
	    
	}
	
	_route.unbind_bind(';hello', tab_change_listener, function() {
	    console.log("Awesome change");
	    _route.refresh();
	});
    },
    
    bind: function (){
	self = this;
	
	$( "a.btn-primary", self.cache_l.c).unbind_bind( "click", function() {
	    var tabname = event.target.rel
	    url = self.urls[tabname]
	    _route.push(url);
	    return false;
	});
 	
	
    }
    
}

ObjectHasCache.call(Hello.prototype);

```

We have bound a click on a.btn-primary inside container to this function.

```javascript

$( "a.btn-primary", self.cache_l.c).unbind_bind( "click", function() {
    var tabname = event.target.rel
    url = 'hello'+self.urls.tabname
    _route.push(url);
   return false;
});
```

In the execution of the callback we do

```javascript

_route.push(url);

```

This pushes a url state. This further triggers the callback we have bound using unbind_bind earlier.

```javascript
	_route.unbind_bind(';hello', tab_change_listener, function() {
            console.log("Awesome change");
            _route.refresh();
        });
```

This runs the tab_change_listener which does everything that we were doing with templates ealier. Without requiring a reload.
