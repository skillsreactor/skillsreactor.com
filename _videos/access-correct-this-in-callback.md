---
name: 'How do I access the correct "this" inside a callback?'
description: >-
    We briefly look at why you might not be getting the values
    you expect when using this inside of callback functions and
    how we can use closures and hard bindings to resolve the problem.
vid: -kOyAMWgL_s
tags:
    - faq
---
## Transcript

Let's look at a constructor function that has a callback inside of its function body. When you're referring to the `this` variable inside of the function, you may not have access to the object that you're newly creating.

In this example, we have an announce method that belongs to the created announcer prototype. Inside of that method, we refer to a specific name property that exists on one of our newly created objects, but when we actually call the method with setTimeout as a part of a callback, the `this.name` property will actually return undefined.

The reason for that, is that when the announce method is called, it is actually called by the global object. Since the global object does not have a name property in this context, `this.name` is then undefined. Even if we weren't calling a method directly, if instead we used an anonymous function, such as in this example, the `this.name` property would still be undefined. Because within the body of the function that `this` would be called by the global object, which would be the window in the case of the browser and `this.name` would still be undefined.

So what are our options when we have a case like this? one option we have is to switch our anonymous function into an arrow syntax. So by removing the function keyword and instead switching over to an arrow syntax that `this` will now refer to its parent lexical scope, so within the body of this callback function `this.name` would now refer to the `this.name` defined in the parent lexical scope.

In the case of a method, we can bind the method to refer to the newly created object. In order to do this we call the bind method and pass in the newly created object to be used as `this` value when this function is called. This is sometimes referred to as a hard binding.

Another option that we have is to store the reference to the newly created object into a variable. We can create a variable here called self and assign the value of `this` into that variable and then within an anonymous function we can use this variable inside the function body to create a closure and capture the value of the `this` keyword inside of that function body.