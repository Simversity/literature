---
layout: page
title: "Keeping states using routes"
description: ""
category: tut

---

### Routes base routing

To understand more about states based routing offered by routes.js. One can look at this documentation of [angular-ui](https://github.com/angular-ui/ui-router/wiki/URL-Routing).

Lets say you are on a link /valid . You have three tabs on it. And you wish to maintain the states in it such that when you refresh a page or something you should reach the same state.

```text
     
     /valid
	  tab1
	  tab2
	  tab3

```

This is handled by routes.js. routes.js provides convinience library for that. In its essence routes binds certain events to url states such that if you arrive at a url state. The events are automatically triggered.


### Finding the url states


Lets say we have an html

```text
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


<?py with capture_as('body'): ?>
<div class="container">
    <div class="svtermsofuse"> <!-- content container -->
      <div class="list-group">

            <li>
           <a href="#" class="list-group-item nav-pills" id="key1">Tab1</a>
            </li>
            <li>
           <a href="#" class="list-group-item nav-pills" id="key2">Tab2</a>
           </li>
           <li>
           <a href="#" class="list-group-item nav-pills" id="key3">Tab3</a>
            </li>



    </div>
    </div>
</div> <!-- end content container -->

<?py #endwith ?>

```

We want to capture the click on each of these tabs as a seperate url state. We first define in blackjack urls/handlers to capture these states.

```python     
Url(r"/valid/(?P<key>key\d+)/?$",
    valid_urls_handlers.ValidUrlstabs, 'valid_urls'),
```

Note that valid_urls is the url name.


Then we bind the url states to a listener.


```javascript
bind_route: function () {

    function fire(obj) {
        $("#"+obj.url_args['key']).parent().css('color: #0000FF');
    }
   
   _route.unbind_bind(';valid_urls', fire);
   _route.refresh()

},
```

```javascript
_route.unbind_bind(';valid_urls', fire);
```
This url binds all valid_urls named urls to the callback, fire i.e whenever the url state is a form of "valid/<key>" the particular callback is triggered.The callback here changes the color of the tab "a" element.

  
### Bringing it all together in the DOM.


```javascript

 bind: function (){
    $( ".nav-pills" ).on( "click", function() {
     targetid = event.target.id;
    _jack.reverse_url('valid_urls', {'key':targetid},
    function (url)
    {
        _route.push(url);

    })

    });
```

Now we have url states and the callbacks they trigger. But we need to bring it all together and link it to events on the DOM. For this we create a binding on the dom on the click event for class nav-pills. Now the events on this DOM are to be connected to the _routes states. For this we use _jack.reverse_url. _jack.reverse_url is a convinience function that converts a named url to the complete url. Here in this example we have bind the click events on the nav-pill tabs to a callback that changes the url state to valid/<key> type urls. 

The callback to change url state:

```javascript
function (url)
    {
	_route.push(url);

    }

``` 

When the state of these urls changes. It triggers the change in url state which further trigger the callback fire which changes the color of the tab. Hence a DOM event is connected to a url state and a callback.



### Conventions for JS code:

init: The contructor for js added. Here all those things should be done that are to be done on page load.


bind: All the bindings should be added in this function by convention.


### The whole thing

```javascript
var Hello = function (o) {

};

Hello.prototype = {
  "change_select": function(cb) {
    var self = this;
    console.log("something")
    $('element').css('color: #0000FF');
    debugger
    },

    "init":  function () {
      this.bind_route();
      this.bind();
    },

 bind_route: function () {

      function fire(obj) {

        $("#"+obj.url_args['key']).parent().css('color: #0000FF');
      }
      _route.unbind_bind(';valid_urls', fire);
      _route.refresh()
    },

 bind: function (){
    $( ".nav-pills" ).on( "click", function() {
     targetid = event.target.id;
    _jack.reverse_url('valid_urls', {'key':targetid},
    function (url)
    {
        _route.push(url);

    })

    });



}

}

ObjectHasCache.call(Hello.prototype);

```
