---
layout: page
title: "Templating Basics"
description: "Add a view to Siminars. Add a url, and an api call"
category: tut
date:  2014-08-29 19:05:07
tags: []
---

### Sample template

```text
<?py _context['_layout'] = 'base.t_html' ?>

<?py with capture_as('js'):?>
#{ the.load_js_dependencies(this, "valid") }
<script type="text/javascript">
  $(function() {
      var _valid = new Valid("${user_id}");
      _valid.init()
      });
</script>
<?py #endwith ?>


<?py with capture_as('body'): ?>
<div class="container">
    <div class="svtermsofuse"> <!-- content container -->

    <div class="list-group">

      <?py for url_key in urls.keys(): ?>
           <a href="#" class="list-group-item">${url_key}</a>

      <?py #endfor ?>




    </div>

    </div>
</div> <!-- end content container -->

<?py #endwith ?>
```

```text
<?py _context['_layout'] = 'base.t_html' ?>
```
Base template to extend.




```text
<?py with capture_as('js'):?>
<?py #endwith ?>
```
Defining the js block


```text
#{ the.load_js_dependencies(this, "valid") }
```

Getting js dependencies for a particular js from js_map.


For more look at [tenjin documentation](http://www.kuwata-lab.com/tenjin/pytenjin-users-guide.html).



### Adding Javascript

In blackjack/utils/tenjin_helpers there is function load_js_dependencies. This function essentially loads the javascript for the siminars project. 
To add a javascript create a map in the file blackjack/utils/js_map.py 

```python
DEBUG_JS = { ....

	"valid":[jack_js/valid.js,
	],
}                                                            
```

To load this javascript on the server side use

```text
#{ the.load_js_dependencies(this, "valid") }
```

This loads the js files defined by the "valid" key in js_map mapping.
