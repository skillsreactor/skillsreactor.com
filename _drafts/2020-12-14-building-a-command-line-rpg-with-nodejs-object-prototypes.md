---
layout: post
title: Object Prototypes | Building a Command-Line RPG with Node.js
author: Aaron Jewell
preview_thumbnail: assets/images/posts/2020-10-13-rpg-main-menu-thumbnail.jpg
excerpt: >-
    Let's explore some Node.js concepts and have a little fun at the same time.
---

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

## Delegating Character Methods to Professions

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