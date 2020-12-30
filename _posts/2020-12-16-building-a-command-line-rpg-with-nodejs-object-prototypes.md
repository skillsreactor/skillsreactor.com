---
layout: post
title: Object Prototypes | Building a Command-Line RPG with Node.js
author: Aaron Jewell
preview_thumbnail: assets/images/posts/2020-10-13-rpg-main-menu-thumbnail.jpg
excerpt: >-
    Now that we have created our Character constructor, we are ready to begin implementing some behavior in the form of prototype methods.
lead: >-
    Now that we have created our Character constructor, we are ready to begin implementing some behavior in the form of prototype methods.
comments: true
recommended_tag: node-cli-rpg
tags:
    - node-cli-rpg
---
 
 The initial release code can be found [here](https://github.com/skillsreactor/rpg-learning-example/tree/1.0.0){:target="_blank"}.

## Table of Contents

[Delegating Character Methods to Professions](#delegating-character-methods-to-professions)

[Adding Methods to Professions](#adding-methods-to-professions)

## Delegating Character Methods to Professions

We'll need a way to do damage to our enemies. Let's implement a `doDamage` method to our Character constructor's prototype. We add methods to the prototype so that all instances of characters will have access to that exact function. If we define the method inside the constructor function, new instances of that function will be created for each instance, instead of a single function that is shared across instances. For our game, since we are only making a single character instance, it won't make too much of a difference, but if we were making many instances from a constructor, having the create new functions for each object can be a big waste of memory.

```javascript
function Character(name, profession) {
    this.name = name;

    switch (profession) {
        case 'Warrior':
            this.profession = new WarriorBehavior(health);
            break;
        case 'Thief':
            this.profession = new ThiefBehavior(health);
            break;
        case 'Mage':
            this.profession = new MageBehavior(health);
            break;
        default:
            throw new Error(`${profession} is not an implemented class`);
    }
}

Character.prototype.doDamage = function(enemy) {
    // Delegate the doDamage method to it's specialized profession doDamage
    this.profession.doDamage(enemy)
}
```

From our last post, we decided the implement combat behavior directly on the character's profession, so the character method will simply delegate the functionality down to a matching method on the profession. We are doing this so we can easily implement profession specific damage calculation mechanics without coupling it to the character objects. Let's look at an example of that with the Thief profession...

## Adding Methods to Professions

```javascript
function ThiefBehavior(health) {
    this.health = health || 75;
    this.attack = 10;
    this.criticalChance = 0.15
    this.criticalModifier = 3;
    this.profession = "Thief";
}

ThiefBehavior.prototype.doDamage = function(enemy) {
    const damage = Math.random() <= this.criticalChance
        ? this.attack * this.criticalModifier
        : this.attack;
    enemy.health -= damage;
}
```

Now the thief will be the only profession who can have a critical attack when doing damage to an enemy. If we later choose to add armor to enemies, we might make the Mage profession's attacks bypass armor reduction because the attacks are magical, for example.

In the next post, we'll be looking at how we are handling our actions in our actions module by implementing a function for each corresponding action choice. The final code for the initial release can be found [here](https://github.com/skillsreactor/rpg-learning-example/tree/1.0.0){:target="_blank"}
