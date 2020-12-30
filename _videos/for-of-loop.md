---
name: What are For ... Of Loops?
vid: rdziKWw8y0g
description: >-
    We examine how for ... of loops are different from
    for ... in loops and how we can use Symbol.iterator
    and generator functions to define our own iterable
    objects to use with these loops.
tags:
    - fundamentals
    - faq
comments: true
---

## Transcript

In addition to arrays, in JavaScript, there are other types of iterable objects, such as Strings, typed arrays (such as the Uint8Array for storing bytes) Maps, Sets, the arguments keyword, a NodeList as part of the dom, and generators. There are a couple ways that we can loop over these. One way that we might use for these type of iterable objects, is a for of loop.

The for of loop was introduced as part of the ECMAScript 2015 standard and it allows us to loop over these iterable objects and get the values contained within. In order for an iterable object to work with the for of loop it must define a Symbol.iterator and this is what allows the for of loop to determine what should be looped over.

You might already be familiar with a for in loop. The for in loop will iterate over the enumerable properties of the object.

So here in this example, we have our alphabet object and it has a dot length property which we've defined as 26. Inside of our for in loop it will console log the length property because that is a enumerable property. But if we use a for of loop, we've defined the Symbol.iterator function to let the for of loop know which values need to be returned when looped over. So when we execute a for of loop against our alphabet object because we've defined this Symbol.iterator generator function what will be returned inside of the loop is the lowercase characters a through z because we are calling String.fromCharCode using 96 plus an offset. So once the while loop finishes we will no longer yield any values from our generator and the for of loop will stop running.

Finally, using a break throw or return within a for of loop will terminate the loop.