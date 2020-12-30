---
layout: post
title: Bringing It Together | Building a Command-Line RPG with Node.js
author: Aaron Jewell
preview_thumbnail: assets/images/posts/2020-10-13-rpg-main-menu-thumbnail.jpg
image: assets/images/posts/node-cli-rpg-bringing-it-together.jpg
excerpt: >-
    With all of our components ready to go, we can bring it together into our command line interface. We'll use inquirer and promise chains to build out our flow.
lead: >-
    With all of our components ready to go, we can bring it together into our command line interface. We'll use inquirer and promise chains to build out our flow.
comments: true
recommended_tag: node-cli-rpg
tags:
    - node-cli-rpg
---

You can follow along with this version of the game code [here](https://github.com/skillsreactor/rpg-learning-example/tree/1.2.0){:target="_blank"}

{%- include contribute.html repo_link="https://github.com/skillsreactor/rpg-learning-example/" -%}

## Table of Contents

[Initialize Our Character From Saved State](#initialize-our-character-from-saved-state)

[The Main Menu](#the-main-menu)

[Handling the Menu Selection](#handling-the-menu-selection)

[Handling the Result of the Action](#handling-the-result-of-the-action)

[Handling Errors](#handling-errors)

[Create Character Interface](#create-character-interface)

[Minimum Viable Product (MVP) Complete](#minimum-viable-product-mvp-complete)

## Initialize Our Character From Saved State

We'll start by looking down the happy path, where the player already has a created character, and they want to play a round of the game.

```javascript
// We bring in our state module
const state = require("./state");

const initializeCharacter = (data) => {
    return new Character(
        data.name,
        data.profession.profession,
        data.gold,
        data.profession.health
    );
}

state.read()
    .then(initializeCharacter)
```

Since our read method of our state module returns a promise, we call it and then executie it's then method, with our new `initializeCharacter` function as its callback. Our state module handles the JSON serialization and deserialization, and gives us back the character object. We use this plain object and make an instance of a Character from it.

## The Main Menu

With our character initialized and ready to go, we can launch the main menu. We'll make a function specifically for it, which takes in a character instance. Finally we'll update our promise chain to call it in sequence.

```javascript
const inquirer = require("inquirer");

const menu = (character) => {
        // if character is found, welcome them back by name
        console.log(`Welcome back, ${character.name}`);
        return inquirer.prompt([
            {
                name: 'main',
                message: 'Choose your action',
                type: 'list',
                choices: ['Fight an enemy', 'Rest', 'Display stats']
            }
        ]).then((answers) => {
            // Pass our state and answers to the next step in
            // the promise chain.
            return { character, selection: answers.main };
        });
}

state.read()
    .then(initializeCharacter)
    .then(menu)
```

We're using [inquirer](https://www.npmjs.com/package/inquirer){:target="_blank"} to handle prompts to our players. It has an elegant promise-based API that fits well in our current setup. Our menu will simply ask the player what action they would like to take this round. They can fight an enemy, rest in exchange for gold, or simply view their character. When we get their answers back, we pass an object consisting of the character and their selection to the next callback in our promise chain. When using promise chains, you can keep chaining calls to `.then()` as long as your callbacks are returning something. You can only return a single value from the callback, so to get around that limitation, it's common to bundle the values together into either an object, or an array. We've used an object in this case, because it's a little more descriptive on the meaning of the values.

{%- include related-video.html vid="QODw6A1rNC0" -%}

## Handling the Menu Selection

We now know which action the player wants to take, so the simple implementation here is to switch on the value, and call the function that handles each of the specific possible actions from our actions module.

```javascript
// Destructuring is used here to get our specific functions from
// our module.
const { attack, rest, printStats } = require("./actions");

// Destructuring again here to get the two data items from
// our last callback.
const handleMenuSelection = ({ character, selection }) => {
    switch (selection) {
        case 'Fight an enemy':
            return attack(character);
        case 'Rest':
            return rest(character);
        case 'Display stats':
            return printStats(character);
        default:
            console.log(character, selection)
            // ...after some undefined outcome
            // return the updated character
            return character;
    }
}

state.read()
    .then(initializeCharacter)
    .then(menu)
    .then(handleMenuSelection)
```

We're using destructuring a couple times in this example, because the containing object (either module or previous callback return value) doesn't have significant meaning to us, we just care about a few specific properties. We return the result from the calls into our actions module, since it will return back the updated stats of our character. We specify a default case here, in case we forget to implement something later. Likely, we'd want to actually throw an error in this case.

## Handling the Result of the Action

In our previous action code, we know that if the character met their fate, we return back null from the action. We need to handle that outcome here so we know whether to persist the updated character, or delete the save file.

```javascript
const handleActionResult = (character) => {
    if (character) {
        return state.write(character);
    } else {
        // you have died... sorry...
        return state.del();
    }
}

state.read()
    .then(initializeCharacter)
    .then(menu)
    .then(handleMenuSelection)
    .then(handleActionResult)
```

Both of these state module functions return promises, but we don't really have anything else to do after this step. We've loaded the saved game, initialized the character, given the player their action choices, handled their choice, and handled the outcome, and now the process will successfully exit. 

With promise chains, we have a single opportunity to handle any promise rejections along the chain. To do that we add a call to `.catch()` at the end of the chain. We ignored that happens when the save file doesn't exist early on, here is our chance to handle that outcome and route them to the create character dialog.

## Handling Errors

In almost every unhandled exception or promise rejection scenario within our small game, we just want to re-throw the error. There is one outcome that has meaning in our game though, and that is when the state module tries to read our save file and it doesn't exist. This can signify the first play-through for a player.

We'll handle this outcome, as well as the case where the save file was found, but simply not parseable by `JSON.parse`

```javascript
const handleError = (err) => {
    // File not found or not parseable
    if (err.code === 'ENOENT' || err instanceof SyntaxError) {
        return createCharacter();
    }
    // All other errors,
    throw err;
}

state.read()
    .then(initializeCharacter)
    .then(menu)
    .then(handleMenuSelection)
    .then(handleActionResult)
    .catch(handleError);
```

We look at the error code and match it against the one that would be returned by a file not found. When `JSON.parse()` can't parse a string correctly, it throws a `SyntaxError` so that will be our clue that we need to make a new save file for that player to get them out of that un-recoverable state. We'll make a call into our new createCharacter method...

## Create Character Interface

This will have a few steps to it, and two of them are asynchronous (prompts, saving). This function will have its own promise chain going so we can handle the asynchronous parts correctly.

```javascript
const createCharacter = () => {
    // Welcome them for the first time
    console.log(chalk.green ('It looks like you are new here.'))
    console.log(chalk.green ("Let's make your character"));

    // offer choices of fighter thief mage
    // fighter - high health, low damage.
    // thief - medium health, critical chance
    // mage - low health, high damage.
    return inquirer.prompt([
        {
            name: 'name',
            type: 'input',
            message: 'What is your name?'
        },
        {
            name: 'profession',
            type: 'list',
            message: 'What is your profession?',
            choices: ['Warrior', 'Thief', 'Mage']
        }
    ]).then(({ name, profession }) => {
        // Create the character object
        const character = new Character(name, profession);

        return state.write(character)
    }).then(() => {
        console.log("Character created successfully")
    }).catch(err => {
        console.error("Unable to save your character.", err)
    })
}
```

We start our with a few messages to the player, and then offer them a prompt for their name, and their character profession. Once we have those steps, we'll build a new `Character` instance for it, and attempt to save the state. If the state save was successful, we let them know, otherwise we let them know that there was an error and display it to the console. We put the success message into its own step in the promise chain just so that we don't display the success before we know the file save worked correctly.

## Minimum Viable Product (MVP) Complete

We can now use our node installation to run the game!

![Gameplay Demo](/assets/images/posts/node-cli-rpg-gameplay.gif)

With everything working as it should, we can tag this v1.0.0 and release. Since the start of this article series, we've already brought in some new features from community members and done a few really interesting improvements to the architecture. Issue creation from the community is highly encouraged and all pull requests will be reviewed and discussed in the friendliest and understanding way. If you haven't contributed to open source projects before, this is a great place to start. Issues are tagged as `good first issue` and everyone of all skill levels is invited to participate.

Let's keep building and growing our skills at the same time!

{%- include contribute.html repo_link="https://github.com/skillsreactor/rpg-learning-example/" -%}