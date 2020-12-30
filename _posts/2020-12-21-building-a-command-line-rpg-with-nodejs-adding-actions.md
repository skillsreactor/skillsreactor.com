---
layout: post
title: Adding Actions | Building a Command-Line RPG with Node.js
author: Aaron Jewell
preview_thumbnail: assets/images/posts/node-cli-rpg-adding-actions-thumbnail.jpg
image: assets/images/posts/node-cli-rpg-adding-actions.jpg
excerpt: >-
    It's time to add some actions for our characters to take. We'll implement a function for each action our character can take.
lead: >-
    It's time to add some actions for our characters to take. We'll implement a function for each action our character can take.
comments: true
recommended_tag: node-cli-rpg
tags:
    - node-cli-rpg
---

## Table of Contents

[Function Definition](#function-definition)

[Making Enemies](#making-enemies)

[Combat](#combat)

[Death](#death)

[Victory](#victory)

[Resting](#resting)

[Printing Character Statistics](#printing-character-statistics)

[Module Exports](#module-exports)

[Next Steps](#next-steps)

## Function Definition

Our action functions will all follow a similar pattern. We'll take in the character object that we created after the game loaded, and return the object back at the end of the action.

```javascript
const attack = (character) => {
    // Battle commences!

    return character;
}
```

Anything that occurs within our action will be expected to directly modify the character, and we'll let the code that called the action handle turning our character back into JSON data and saving it for the next round.

Throughout these actions we are going to be using [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals){:target="_blank"} to easily place values from our variables and JavaScript expressions inside of strings. This greatly simplifies generating our console messages to the player, which we would otherwise need to build up using the concatenation operator, `+`.

Our functions do not reference the `this` keyword inside of their function bodies, so it is perfectly fine to use arrow expressions. Because these functions are declared outside of any other function declaration, any use of the `this` keyword would refer to the global object.

{%- include related-video.html vid="fQ6QP6P8u3Y" -%}

## Making Enemies

Our enemies start out very simple. They are just a combination of damage, health, and gold. In order to add some variety to gameplay we are randomly assigning the damage and health of the enemy between a specified upper and lower limit.

```javascript
    const damage = Math.floor(Math.random() * 10 + 1); // 1 - 10 damage
    const health = Math.floor(Math.random() * 10 + 21); // 10 - 30 health
    const gold = health + damage;
    
    const enemy = { damage, health, gold };
```

We calculate the gold reward for the enemy as the sum of their health in damage, so that players are better rewarded when fighting more difficult enemies.


{%- include related-video.html vid="lHIISpozFDI" -%}


## Combat

Our combat will play out, without breaks, in a single loop until either our fearless character or the enemy have run out of health.

```javascript
while (character.profession.health > 0 && enemy.health > 0) {
    const prevEnemyHealth = enemy.health;
    
    character.doDamage(enemy);
    console.log(chalk.bold.red(
        `You hit the enemy for ${prevEnemyHealth - enemy.health} damage.`
    ));
    
    character.receiveDamage(enemy.damage);
    console.log(chalk.bold.magenta (
        `The enemy hits you for ${enemy.damage} damage.`
    ))
    
    console.log(
        `You have ${character.profession.health} hitpoints remaining.`
    );
}
```

We don't know ahead of time how much damage the character will do to the enemy. So in order to report back to the player how much damage was done, we store a reference to the enemies health before any damage is done. Then after the damage has occurred, we look at the difference between the two in order to determine damage done. [Chalk](https://www.npmjs.com/package/chalk){:target="_blank"} is a NPM module that will make our command line output more engaging and was added to the game from a [pull request](https://github.com/skillsreactor/rpg-learning-example/pull/13){:target="_blank"} by community member [mdurst365](https://github.com/mdurst365){:target="_blank"} resolving this [issue](https://github.com/skillsreactor/rpg-learning-example/issues/9){:target="_blank"} .


## Death

If the character ran out of health before the enemy... then we have met our fate. We let the player know about this, and now thanks to the [pull request](https://github.com/skillsreactor/rpg-learning-example/pull/11){:target="_blank"} from community member [Oliver Jacob](https://github.com/Oliverjacobb){:target="_blank"} resolving [this issue](https://github.com/skillsreactor/rpg-learning-example/issues/10){:target="_blank"}, we also print the final stats of the character so the player can remember their brave hero.

```javascript
console.log(chalk.inverse("You have died. :-X Your final stats were..."));
printStats(character);
return null;
```

We also return null at this point, because our character has ceased to be. We don't want to record our character back into the save with negative health.

## Victory

When we are victorious in combat, we let the player know that they've won, and how much gold they will be rewarded with. We increase their gold by that amount, and let them know how much health and gold they currently have after the battle.

```javascript
console.log(`You have defeated the enemy and received ${gold} gold!`)
character.gold += enemy.gold;
console.log(`You now have ${character.profession.health} health and ${character.gold} gold.`);
```


## Resting

Our resting mechanic is a good way for us to "recycle" the gold we're awarding to players on combat. They can trade in 50 of their gold to regain 25 health. We let the character know about the exchange, and give them their updated health and gold values.

```javascript
const rest = (character) => {
    character.gold -= 50;
    character.profession.health += 25;

    console.log(chalk.yellow("You have gained 25 health for 10 gold."));
    console.log(`You now have ${character.profession.health} health and ${character.gold} gold.`);

    return character;
}
```

You may have noticed that the message at the end of rest, and the one for combat victory are very similar. In programming, we try to follow a principle know as DRY (Don't Repeat Yourself). We could make this code a little DRYer by making a function that we could call to generate this message... but, we're going to be exploring a better way to do messaging in an upcoming version. For now, we'll leave it as it is, since it's only a small amount of repetition.

## Printing Character Statistics

We need a way to display our characters to the screen for the player to see. Our print stats function will use [recursion](https://developer.mozilla.org/en-US/docs/Glossary/Recursion){:target="_blank"} to loop over the object and any nested objects, and console log out the key and value for each non-function property.

```javascript
const printStats = (character) => {
    const recursivePrint = (obj) => {
        for (var key in obj) {
            if (typeof obj[key] === 'object') {
                recursivePrint(obj[key])
            } else if (typeof obj[key] !== 'function') {
                console.log(`${key}: ${obj[key]}`);
            }
        }
    }

    console.log("Character Stats");
    console.log("---------------");
    recursivePrint(character);

    return character;
}
```

In order to get the properties of the character object, we're using a for...in loop. Many times when you see this loop, you would see a line similar to:

```javascript
if (obj.hasOwnProperty(key)) {
    // everything else inside the for loop
}
```

That ensures we are only considering properties that exist directly on our object, and not any of the objects in its prototype chain. Our character constructor function that we made previously does not have anything besides the Object built-in primitive, so we don't have that concern here. If we start to sub-class our character objects, or extend it from something like the [Node.js EventEmitter](https://nodejs.org/dist/latest-v14.x/docs/api/events.html#events_events){:target="_blank"} class, we might want to consider doing a check. Another option is for us to use [Object.getOwnPropertyNames](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames){:target="_blank"} method and loop over the resulting keys, and get their values.

## Module Exports

The final step in our actions module is to decide what we want to export, and how we want to structure that. We'll expose each of our functions directly on the exports object, by the same name that we have used to define them within the module. If this object literal declaration looks odd to you, we are using the short-hand syntax that was added as a part of ES2015 (ES6) that allows us to not have to specify `attack: attack` if the name of the property is the same as the variable name that refers to the value.

```javascript
module.exports = {
    attack,
    rest,
    printStats
};
```

## Next Steps

We'll need a way to persist our character since each round of the game consists of a single action. We can't just keep our character in memory. The simplest solution for us at the moment will be to turn the character into a JSON string and write it into the file system as a `.json` file. Node.js provides us a module to do precisely that, and we'll build our module around it.

One the [issues](https://github.com/skillsreactor/rpg-learning-example/issues){:target="_blank} page of the repository, we have some community members working on new features such as predefined enemy types, a leveling system, a `tavern` action, and more! If you have a feature, bug report, or enhancement you'd like to add... head on over to the repository and join the community!