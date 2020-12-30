---
name: How Does the "this" Keyword Work?
description: >-
    A brief introduction to the this keyword in JavaScript and
    the different ways it's value inside of functions can change
    at run-time.
vid: l-BGr-h5HNE
tags:
    - faq
comments: true
---
## Transcript

One of the more confusing aspects of javascript is the this keyword. It's important to note that the this keyword does not get bound when the function is defined but instead is bound when the call actually happens.

Let's consider this constructor function "Parrot" and we're setting the name to the `this.name` and then we have a method here called squawk which will console log out the name followed by "wants a cracker". So when we think about what is the `this` keyword going to refer to both within the constructor function itself and within the method set on the prototype.

In order of importance of how `this` is determined, the first one would be when we "new" up a new object. When we use the `new` keyword on our constructor function, inside of that function `this` refers to the newly created object, this "polly" object that we've created.

The next rule of importance further down is considering whether or not call `apply` or `bind` were used when calling this method so all three of these methods are a part of the function prototype and in the case of call it will swap the methods object with the object that is provided. So in this case when we call poly squawk and we pass in an object, poly will be swapped out with the object that we provided. In the case of apply, we'll call the method and we'll swap `this` reference inside of the method with the object that we supplied in the first argument to apply. Finally with bind we are creating a hard binding to that method so anytime that method is called in the future that `this` inside of that method will refer to the object that we've passed in here.

The next thing we need to consider was the method called within object context. So in the case of our squawk method because the function is a method on the parrot prototype, there is an object context within that method and so when we call squawk this will refer to the object that is bound to that method so in this case poly when we have a function that is not provided in object context such as our parrot constructor function without the `new` keyword. In that case, in strict mode, that `this` inside of that function would be undefined and if we were not in strict mode this would refer to the global object. So in the case of a browser it would refer to the window.

One other thing that's important to note is that with the addition of es2015 we have this fat arrow syntax when `this` keyword is used inside of the fat arrow syntax body it will inherit the `this` from its parent lexical scope and use whatever `this` is within that current context.