
---
layout: post
title: "Add/Remove Classes with Raw Javascript"
date: 2010-05-19 10:19
comments: true
categories: [Code, js, jquery]
description: Jquery and other sweeto js frameworks have some awesomeness to help you change the css classes of various elements on the page with ease and grace.  They ha
keywords: javascript, jquery
published: true
---

Jquery and other sweeto js frameworks have some awesomeness to help you change the css classes of various elements on the page with ease and grace.  They have some very convenient functions to add and remove classes.  These are uber-useful and highly recommended for saving you precious time and jiggawatts, but if you don't have jquery available to you, sometimes you may want to do this in raw javascript.  
<!--more-->

```javascript
$("#peace").addClass('be-still');
// or
$("#peace").removeClass('be-still');
```

And now for raw power and lack of ease and grace:

```javascript
document.getElementById("peace").className = "be-still";
```

If you want to add or remove your own, you have to write that goodness, and here's one implementation example for your souit pleasure:

```javascript
function hasClass(ele,cls) {
  return ele.className.match(new RegExp('(\\s|^)'+cls+'(\\s|$)'));
}

function addClass(ele,cls) {
  if (!hasClass(ele,cls)) ele.className += " "+cls;
}

function removeClass(ele,cls) {
  if (hasClass(ele,cls)) {
    var reg = new RegExp('(\\s|^)'+cls+'(\\s|$)');
    ele.className=ele.className.replace(reg,' ');
  }
}
```

Called like:

```javascript
var ele = document.getElementById("peace");
addClass(ele, "be-still");
// or
removeClass(ele, "be-still");
```


  
