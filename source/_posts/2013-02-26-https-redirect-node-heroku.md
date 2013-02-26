---
layout: post
title: "Https Redirect for Node on Heroku"
date: 2013-02-6 07:33
comments: true
categories: [Code, js, nodejs, heroku, express]
description: Make a NodeJs app on Heroku redirect to an https address when it is not using one.
keywords: js, nodejs, node, heroku, https, https redirect, express
published: false
---

When your app requires users to be using the `https` protocol to make requests and get responses, it's helpful to have an automatic redirect so the user is always in the right place.  Here's how to do that on Heroku.

![Alt Text](http://i.imgur.com/M0kYA.jpg)

<!--more-->

## Secure Express Requests

The [Express](http://) request object has a bunch of great information.  One of its attributes is `secure`.  It's a boolean.  Usually, `secure == true` will mean that you're on `https`.  When that is not true, you're ready for a redirect.  

Unfortunately, for apps hosted on Heroku, `request.secure` will always be false.  The way that Heroku routing works, it will just never be set.  

## Heroku Https Header

Instead, Heroku forwards an http header that allows us to do the same "is secure" test.  On Heroku, `req.header('x-forwarded-proto')` will contain the actual protocol string.
