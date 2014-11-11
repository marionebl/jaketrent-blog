---
layout: post
title: "Set State in Callbacks in React"
date: 2014-06-23 08:30
comments: true
categories: [Code, js, react, superagent]
description: Help your React components set state safely in callbacks
keywords: js, react, components, setState, componentWillUnmount, componentDidMount, unmounted
published: true
image: http://i.imgur.com/DXuSNbw.png
---

In React, you can setup components that fetch their own data and set their own state with that data.  Because of the async nature of data fetching, you’ll have to make sure to keep things cleaned up to avoid the error of trying to set state in an unmounted component.

![React](http://i.imgur.com/DXuSNbw.png)

<!--more-->

## React Components setState

If a React component fetches its own data, it will usually do so in the `componentDidMount` method.  In the data request callback, it will set its own state via the `this.setState` method.  (`this.state` should be treated as immutable.)  State should only be set on mounted components, or those components that are inserted into the dom.  
 
If you attempt to set state on an unmounted component, you'll get an error that looks like this:

```
Uncaught Error: Invariant Violation: replaceState(...): Can only update a mounted or mounting component. 
```

Others have reported this variation:

```
Invariant Violation: replaceState(...): Cannot update while unmounting component. This usually means you called setState() on an unmounted component.
```

Both indicate that you're setting state on a component that is not mounted.  How should one avoid this?  In my case, `setState` was being called in a callback that was firing after the component that initially started the request had already been unmounted from the dom.  Here are the two ways I addressed the problem...

## Assure Component isMounted

If the component is mounted, `setState` is a safe bet.  If it's not mounted, never do it.  If it's not mounted, you probably don't about the state at that point either.  So wrap your state setting in `this.isMounted()`:

```js
React.createClass({
  componentDidMount: function () {
    MyModel.find(function (err, data) {
      if (this.isMounted()) {
        this.setState({ 
          goodiesFrom: data 
        })
      }
    }.bind(this))
  }
  // ...
})
```

It feels a bit like a hack, but it makes the code safe.  Another option...

## Abort the Request

My asynchronous action is a network request that, when finished, will call the callback function.  When the component unmounts, I can just throw away the request so the callback is never invoked.  To do this, we'll take advantage of another React lifecycle hook, `componentWillUnmount`:

```js my-component.js
React.createClass({
  componentDidMount: function () {
    this.req = MyModel.find(function (err, data) {
      this.setState({})
    }.bind(this))
  },
  componentWillUnmount: function () {
    this.req.abort()
  }
  // ...
})
```

In my model, I'm using the [superagent](https://github.com/visionmedia/superagent) library for network requests which provides an [abort](http://visionmedia.github.io/superagent/#aborting-requests) method.

```js my-model.js
var request = require(‘superagent')
MyModel.prototype.find = function (done) {
  return request(‘modelUrl’)
    .end(function (err, data) {
      if (data) {
        done(err, data.body)
      }
    })
}
```

Also note that in my `end` function callback, I'm checking for the existence of data.  This is because when a request is aborted, data will come back as undefined.  

My favored method for solving the problem is the latter request abort method.  It feels cleaner that we're relying on the lifecycle functions of the component to deal with cleanup, much like we would for [events](http://facebook.github.io/react/tips/dom-event-listeners.html).  How have you dealt with this problem?