---
layout: page
title: "Tips and tricks"
description: ""
category: tips
date: 2014-08-29 15:05:08
tags: []
---

### Running multiple callbacks and collating the data



```javascript
var callers = 3;
var returns = 0;
var all_urls = [];
 
function final_callback() {
    console.log(all_urls);
}
 
function keep_checking() {
    returns++;
    if (returns < callers) {
        return;
    }
    final_callback();
    return;
}
 
 
_jack.reverse_url("my_profile", function(url) {
    all_urls.push(url);
    keep_checking();
});
 
 
_jack.reverse_url("my_profile", function(url) {
    all_urls.push(url);
    keep_checking();
});
 
_jack.reverse_url("my_profile", function(url) {
    all_urls.push(url);
    keep_checking();
});


```



### Explanation


This callback to all the _jack.reverse_url calls. Calls keep_checking which is not executed till it ensures that all the callbacks are executed. Then it finally runs the callback. This is quite useful in situations where you are collating data from multiple callbacks.