---
layout: post
title: Constructor Functions | Building a Command-Line RPG with Node.js
author: Aaron Jewell
tags:
    - node
preview_thumbnail: assets/images/posts/2020-10-13-rpg-main-menu-thumbnail.jpg
excerpt: >-
    Let's explore some Node.js concepts and have a little fun at the same time.
recommended_tag: node
---

We're going to build the first module in our command line RPG. In the first
of this series, we described the game we're going to make. We want players
to be able to create a character, fight off enemies for gold rewards, and
trade in that hard-earned gold for rest to replenish their health pool.
To make the game interesting, we are going to make three professions: Warrior,
Thief, and Mage. They will have differing health and attack totals and the
thief will have the ability to critically strike enemies at random.

We decided that to implement the character and professions, we are going
to use constructor functions so that we can quickly initialize instances
of our associated properties and methods. We also decided to favor object
composition over inheritance, so our professions will not be character
sub-classes. Let's start with Character.

## Table of Contents

[Constructor Functions](#constructor-functions)

[Using Behaviors for Character Profession](#using-behaviors-for-character-profession)

[Adding Dynamic Behaviors to Character Construction](#adding-dynamic-behaviors-to-character-construction)

## Constructor Functions

Constructor functions give us a template for an object that we can use
to quickly create new instances. These functions by convention will start
with an uppercase letter, but that is not required by the language. When
the `this` keyword is used inside of a constructor function, it will correctly
refer to the new object instance that will be created. There is nothing
special about the syntax of a constructor function, the real magic happens
when it is combined with the `new` keyword. If a constructor function is
called without using the new keyword before the function call, the `this`
keyword will refer instead to the window (browser) or the Node.js global
object, and no object will be returned. 

```javascript
function Character(name, profession) {
    this.name = name;
    this.profession = profession;
}
```

Our character constructor will start out by taking a name parameter, which we
will use when the player creates their character, and also a profession string
so that we know what profession the player has chosen. We take both parameters
at the same time because we are anticipating prompting the user for both pieces
of information at character creation time. With the bare minimum attributes
of the character captured, let's move on to specializing with a profession.

## Using Behaviors for Character Profession

We're going to use a form of object composition and compose our player of a
specialized profession class. We'll call these behaviors, and specify the
combat-specific stats on that object, as the way the character approaches
combat will be depedent on their profession.

```javascript
function ThiefBehavior() {
    this.health = 75;
    this.attack = 10;
    this.criticalChance = 0.15
    this.criticalModifier = 3;
    this.profession = "Thief";
}
```

The thief behavior, above, has some special parameters related to their unique
mechanic, the ability to critical hit. `criticalChance` will represent the
percentage chance of landing a critical hit, and `criticalModifier` shows
the multiplier we will apply to the attack damage. We'll use those parameters when
calculating the amount of damage the player has done for a combat round. We
also will need to display the profession name in our interface, so we add
a `profession` property to capture it for later.

```javascript
function WarriorBehavior() {
    this.health = 100;
    this.attack = 10;
    this.profession = "Warrior";
}

function MageBehavior() {
    this.health = 50;
    this.attack = 15;
    this.profession = "Mage";
}
```

## Adding Dynamic Behaviors to Character Construction

Now that we have our professions set up, let's create them in response to the
argument provided to our characters profession parameter.

```javascript
function Character(name, profession) {
    this.name = name;

    switch (profession) {
        case 'Warrior':
            this.profession = new WarriorBehavior();
            break;
        case 'Thief':
            this.profession = new ThiefBehavior();
            break;
        case 'Mage':
            this.profession = new MageBehavior();
            break;
        default:
            throw new Error(`${profession} is not an implemented class`);
    }
}
```

At the moment, we have a relatively small finite number of professions that the
player can select. That makes the `switch` statement very appealing to use for
this purpose. It's very important to either `return` a value or `break` in each
case of the `switch`. Otherwise, each case after the first matching case will
also execute. This is oftentimes not the behavior we want. Usually, we want
just the matching case to execute, and nothing else.

For simple input validation, we set a default case that will `throw new Error()`
in the event that we manage to pass an invalid profession into the constructor
function. The `Error` shows another constructor function at work, utilizing the
`new` keyword! To compose a descriptive error message to the caller, we're
using a template literal to insert the profession the caller tried to use.
Remember, when using template literals, wrap the string in backticks \`
(usually next to the `1` key on English keyboards) and variables or expressions
are inserted into `${}` within the string. By combining the error construction
with throw, we're going to pass the `Error` back up the call stack to
most likely result in an unhandled exception, letting the caller know the
mistake they've made.

Now that we have our basic character set-up, and our dynamically assigning
their profession based on the constructor call arguments, we can move onto
the more interesting part... behavior. For this, we'll look at object
`prototypes` and how we can set methods that instances of objects created
with our constructor functions can access.