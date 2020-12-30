---
layout: post
title: Filesystem Module | Building a Command-Line RPG with Node.js
author: Aaron Jewell
preview_thumbnail: assets/images/posts/2020-10-13-rpg-main-menu-thumbnail.jpg
image: assets/images/posts/node-cli-rpg-filesystem-module.jpg
excerpt: >-
    Time to persist the character between rounds of the game. We'll build a state module using the native Node.js "fs" module.
lead: >-
    Time to persist the character between rounds of the game. We'll build a state module using the native Node.js "fs" module.
comments: true
recommended_tag: node-cli-rpg
tags:
    - node-cli-rpg
---

We'll want to make this module relatively generic, so that we can look at other ways to persist the game state in future iterations, such as cloud storage.

{%- include contribute.html repo_link="https://github.com/skillsreactor/rpg-learning-example/releases/latest" -%}

## Table of Contents

[What We'll Build](#what-well-build)

[Add the Read Method](#add-the-read-method)

[Add the Write Method](#add-the-write-method)

[Add the Delete Method](#add-the-delete-method)

[Promisify the Exports](#promisify-the-exports)

[Next Steps](#next-steps)

## What We'll Build

From our initial planning, our state will need to have three pieces of functionality: Reading our saved character, writing our character to the filesystem as a JSON file, and deleting the saved file when the character has been defeated. Node.js provides a native module for all three of these tasks, so we'll just make some wrapper functions around these that will make assumptions about the filename, and serializing/deserializing it through the [JSON global object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON){:target="_blank"}.

```javascript
const fs = require('fs');
```

We import the [filesystem module](https://nodejs.org/dist/latest-v14.x/docs/api/fs.html){:target="_blank"} using the CommonJS module syntax of Node.

## Add the Read Method

The read method for our state module will accept a callback, although by the end of the module, we'll be switching it over to use a [native JavaScript Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises){:target="_blank"}. We're going to use the asynchronous version of the [readFile method](https://nodejs.org/dist/latest-v14.x/docs/api/fs.html#fs_fs_readfile_path_options_callback){:target="_blank"}, instead of the synchronous version. Accessing the file system is a very slow operation in computer speed terms, so we don't want to block the node process while doing it. 

```javascript
// Attempt to read the state file, and parse it into an object
// to return to caller.
const read = (callback) => fs.readFile("save.json", (err, state) => {
    if (err) {
        // File could not be read... not found, bad permissions,
        // out of file descriptors on OS...
        return callback(err)
    }
    try {
        // Attempt to parse the contents of the state file.
        return callback(null, JSON.parse(state));
    } catch (err) {
        // Unable to parse, return the error to caller.
        return callback(err);
    }
});
```

We'll be calling our passed in callback within the callback we provide to the readFile method. Because readFile uses an error-first style callback, like native methods in node, we first check to make sure that the readFile was successful, and if not, we pass it in as the first argument to our methods callback.

If we got the game state successfully from the file, we try to parse it and pass it back to our callback. We wrap this is a try catch, so that if parsing the JSON fails, we can pass that error along in our callback. When using the error-first style callback, it is usually convention to not throw unhandled exceptions, but to instead pass them along through the error callback. We follow that here and catch the failure of the JSON parsing, if it happens.

## Add the Write Method

Our write method is very similar to our read function. We take in a state to save, and a callback for when the task is complete

```javascript
// Write the stringified state object to the save file.
const write = (state, callback) =>
    fs.writeFile("save.json", JSON.stringify(state, null, 2), callback);
```

We pass some additional arguments into JSON.stringify, so that the file that is written is indented and easily readable by humans. That will make debugging easier, but could be easily removed in the future. We've hard-coded the filename directly into our method, using the same filename that we used in the read method.

We're reading/writing into the local directory, but in the future we may want to use the Application Data folder provided by operating systems so that we can ensure we have read/write access and persist some data between installations.

## Add the Delete Method

The delete method is a very simple wrapper around the `fs` module's [unlink method](https://nodejs.org/dist/latest-v14.x/docs/api/fs.html#fs_fs_unlink_path_callback){:target="_blank"}. We simply add our hard-coded filename into our wrapper.

```javascript
const del = callback => fs.unlink("save.json", callback);
```

## Promisify the Exports

I generally find promises easier to work with, and it helps to avoid "callback-hell" or "pyramids of doom". For that reason, we'll use the native `util` module to convert our functions that accept error-first style callbacks into functions that return promises. Then if we decide we want to switch these back to callbacks, or expose their callback versions, we can easily change it.

```javascript
module.exports = {
    read: util.promisify(read),
    write: util.promisify(write),
    del: util.promisify(del)
};
```

The util library exposes a [promisify](https://nodejs.org/dist/latest-v14.x/docs/api/util.html#util_util_promisify_original){:target="_blank"} for exactly this purpose. There is also an accompanying function for swapping functions that return promises into ones that take callbacks ([callbackify!](https://nodejs.org/dist/latest-v14.x/docs/api/util.html#util_util_callbackify_original){:target="_blank"})

## Next Steps

Our next step will be to tie our character, actions, and state together and bring in the [inquirer npm module](https://www.npmjs.com/package/inquirer) to build out some prompts to build up our characters, and display some simple menus for the player to give them a way to choose one of our available actions.

{%- include contribute.html repo_link="https://github.com/skillsreactor/rpg-learning-example/releases/latest" -%}