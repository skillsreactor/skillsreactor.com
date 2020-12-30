---
layout: post
title: Constructor Functions | Building a Command-Line RPG with Node.js
author: Aaron Jewell
tags:
    - node
preview_thumbnail: assets/images/posts/2020-10-13-rpg-main-menu-thumbnail.jpg
excerpt: >-
    Let's explore some Node.js concepts and have a little fun at the same time.
---

## Constructor Functions

```javascript
function Character(name, profession) {
    this.name = name;
    this.profession = profession;
}
```

## Using Behaviors for Character Profession

```javascript
function ThiefBehavior(health) {
    this.health = health || 100;
    this.attack = 10;
    this.profession = "Warrior";
}
```

## Adding Dynamic Behaviors to Character Construction

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
```