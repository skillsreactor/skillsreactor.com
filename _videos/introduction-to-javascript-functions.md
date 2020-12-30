---
name: Introduction to JavaScript Functions
description: >-
    We provide an introduction to the syntax used to define and call
    functions in our JavaScript code. We briefly cover lexical scope
    and how to return values from our functions
vid: IY-CO1dz8rE
tags:
    - fundamentals
comments: true
---

## Transcript

Functions are an incredibly useful feature in programming. When we have a procedure that needs to happen more than once, we can use functions to define that procedure and then execute that code by calling the function whenever we need it. That means we have less code to write and maintain and less surface area for bugs!

We'll start by defining a function. We use the function keyword to start and then optionally write a function name. After our function name, we provide a pair of open and closed parentheses. This is where we place any parameters that our function will need. We use parameters to allow our function to modify its behavior based on some external input when our function is invoked, or run in other words. Values are passed into our function as arguments. The code we write within our function will have access to the values bound to these parameters. You might not need any parameters in your function. Typically this occurs when your function has side effects that act on global variables or user output, such as the console or the page.

Next, we can start to specify the code to execute for our function. You can use multiple lines and statements within the code block and even define functions inside. Any variables that are declared inside of the function, as well as the parameters of the function, are available both inside the function block and inside nested functions. This is a concept known as "lexical scope". Code outside the function will not have direct access to these values.

There is a special keyword, return, which you can use to pass data outside of your function as a final step. Once you specify the return of the function you won't be able to define any further behavior. The function will end and the value of the return expression will be passed outside the function call.

If a name is not provided, we call this an anonymous function. Anonymous functions are very common in JavaScript especially when we need to define some behavior that occurs after a specific event takes place.

When you are ready to execute your function, you first specify the function name followed by an open and closing parenthesis. This is where you would pass in any arguments that will be bound to the function parameters. It is not necessary to call a function with its full set of parameters. If the code in the body of the function does not require the parameter to have a value, there is no requirement to specify an argument for it. When the function is called remember to capture the value of the return by assigning the output of the function call into a variable. This is very common to do, but is not required if you do not need to know the return value. You can assign the output of a function call to a variable to store it or use it in other places where you would place a javascript expression, such as a conditional statement.

In addition to the functions we defined in our code there are many functions provided to us, both from the language as well as the environment where our code is running. In the browser, there are many host objects that provide us with functions to interact with the browser and in Node.js there's a standard library of modules containing useful functions. We can call these functions exactly the same way as the functions we define

Remember these functions are a fantastic way for us to define some behavior that can be reused by many parts of our application when you find yourself writing the same section of code more than once that should be an indicator to you that it might be time to define a function for that task