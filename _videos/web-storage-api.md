---
title: Web Storage API
vid: ojfeYawrk0Y
tags:
    - web-api
description: >-
    A short introduction for beginners on how to use the web storage API
    with JavaScript.Specifically through calling the getItem and setItem
    methods on the localStorage host object.
comments: true
---

## Transcript

There may be times we want to store some of our data or application state between page loads. When we want to do that one of our options that we have available is local storage. So, in this case let's keep track of the number of page visits from a user's browser. We'll start with this visit zero and instead this time we will say `localStorage.getItem()` and we'll pass in a key string which we will call visits. Then if this item existed within local storage it would retrieve our value, but if the value wasn't found we'll set the visits equal to one, otherwise we'll increment the previous number of visits by one. And then finally, let's set our new updated visits back into local storage. so we'll say `localStorage.setItem` pass in our key string which is visits and then our updated value. One thing we want to note is that we need to be careful here, because accessing local storage may not be available in the user's browser. So we would want to check to make sure that local storage existed. In some cases it's possible for an exception to be thrown, so we need to handle that as well. But for this simple example we're going to ignore that fact. Finally, we need to remember that all values that are set into local storage are turned into strings. So if we want to set an object or an array into local storage we'll need to use `JSON.stringify` or `JSON.parse`. So in our case we'll look at this announcement and if we wanted to save this into local storage we would have to say `localStorage.setItem('announcement')` is our key and then we'll say JSON.stringify('announcement'). Then if we wanted to retrieve that value later, we would have to run it through `JSON.parse` `localStorage.getItem('announcement')`. So if we simply console log that statement, we can check that in our browser. If we go over to our browser and we first save the page we can see that the visits key is correctly set to one and it's stored and our announcement has now been set to message. Our item was successfully saved and if we look in the console we can see that it successfully parsed back into an object.