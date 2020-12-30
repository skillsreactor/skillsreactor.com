---
layout: post
title: Getting Started | Building a Command-Line RPG with Node.js
author: Aaron Jewell
preview_thumbnail: assets/images/posts/node-cli-rpg-getting-started-thumbnail.jpg
image: assets/images/posts/node-cli-rpg-getting-started.jpg
excerpt: >-
    We explore some Node.js and JavaScript concepts through the process
    of building a beginner friendly command line RPG game. Let's begin
    by thinking about our plan and how we can structure the components...
lead: >-
    It's always a good idea at the start of a new project to take plenty of
    time to consider what you are building and how to structure your code.
    Spending the time up front to get the design right will usually save time
    during implementation.
tags: node-cli-rpg
recommended_tag: node-cli-rpg
comments: true
---

First, let's go over what we're going to build. We want to make a command line
node application, that allows a player to create a character and battle
enemies. Each enemy that they battle will reward them with coins, which can
be spent to rest in order to heal their wounds from combat. The game is
intentionally very small and open-ended as we want it to serve as a minimum
viable product (MVP) and get a foundation for others to add features to.

{%- include contribute.html repo_link="https://github.com/skillsreactor/rpg-learning-example/releases/latest" -%}

## Table of Contents

[User Interface](#user-interface)

[Persisting Player Characters](#persisting-player-characters)

[Character Diversification](#character-diversification)

[Taking Actions](#taking-actions)

[Enemy Combat](#enemy-combat)

[Resting](#resting)

[Displaying Player Stats](#displaying-player-stats)

[Next Steps](#next-steps)

## User Interface

Our users are going to execute our program each time they want to take a new turn.
If they already have a saved character, then we want to present them with the
possible character choices right away. If we can't find or read a save file, then
we'll want to allow them to make a new character, and present them with choices
for a name and a list of professions to choose.

## Persisting Player Characters

Since each run of the application will consist of a single player turn. We
will need a way to save the game state at the end of every turn, and recall
that game state when the application launches. The simplest way is to save
the state into a file, and read from the file system on application start.
This will also allow us to explore the native filesystem module in node.

We have lots of options on ways to store data. We could have selected a
remote file store, such as [AWS S3](https://aws.amazon.com/s3/), a simple
database such as [SQLite](https://www.sqlite.org/index.html), or
a key value data store like [LevelDB](https://github.com/google/leveldb). We
are going to keep it simple for as long as we can and defer the systems
choices until we can no longer avoid the decision any longer.

This functionality will be a good candidate for it's own module. It isn't
specific to the flow of the game. We know that we will need to store
and retrieve game state, but the mechanics of how it happens should not
have an impact of the rest of the game. That is a good indicator that it
can be split into it's own module.

## Character Diversification

Since our sole game mechanic is fighting enemies, and player
choices consist of their name, profession, and whether to rest or fight
their next battle... It's important that the choice of profession conveys
some meaningful aspect of diversification or it wouldn't feel impactful
to the experience.

We'll offer the profession choices of warrior, thief, and mage. The warrior
will have a higher starting health pool, but lower attack. The thief will
have the ability to randomly critically hit when attacking enemies. Finally,
the mage will have a lower starting health pool, but a higher attack.

The character and their chosen profession can be represented well through
classes as we'll have some instance specific attributes, such as health,
attack, and gold. We may choose to use sub-classing (inheritence) for the
professions, or take a more composition-centered approach and make them
behaviors that we assign to the characters. No matter the approach, this
feel like a good candidate for another module.

## Taking Actions

We want to present a pre-defined list of actions to the user when they have
already created a character. We'll do this through a simple list in the command
line and allow them to use their arrow and enter keys to select the choice. For
the simplest version of our game we'll allow them to: enter combat, rest, and
view player stats. After making their choice, the action will execute, we will
display any results, and end the program.

An enhanced user experience might keep the program running and returning them
to the choice screen after each choice and only ending the program with a
specific choice to quit (or terminating the program). Our goal is simplicity, so
we'll reject that direction for now.

This menu and determing which actions to take and how to execute them will
likely be the main module of our application. It will need to know about
quite a few of our other components, and interfaces tend to be a good
entry component in code.

The actions logic should likely go in it's own module. We should be able to
establish a nice API for interacting with each action, and if they were to
grow more complicated, we could divide them further and place each in their own
module. When each action is selected, our interface code will likely call into
an actions module, and get the result of taking that action back. If we were
to add new actions in the future, this structure would make extending the
user interface trivial.

## Enemy Combat

Since this is our sole game mechanic, like in all good games, we'll
need an element of randomness. We don't want all of our enemies to
be the same strength when we fight them, so randomizing their survivability
and attack values would be a good way to alter the experience of each round.

There are lots of other ways we can design our enemies: We could set up
types of enemies that are stronger or weaker to the various professions, in
a rock-paper-scissors type setup. We could add the concept of armor or the
chance of missing on attack. We are again going to stick with the simplest
solution. We'll randomize the attack and health within pre-defined bounds
and add them together for a gold reward to the player for defeating them.

## Resting

After a few rounds of fighting, we can expect our player character to need
to recover some of their hit point pool. We allow them to choose to rest
for a round for a fee of gold. This provides a recycling mechanic for the
gold reward and allows the character to progress to the next fight.

This is a very simple way to add the mechanic, and there are many ways
we could continue from here. Allowing them to select the rest duration...
allowing them to level up on resting... healing status ailments on sleep...
We are going to stay true to the MVP and keep this simple.

## Displaying Player Stats

We need a way for the player to check how much gold or health they
currently have so that if they haven't played the game in a while,
they can make an informed choice on what their next action should be.
We'll expose the choice to do this alongside the other choices of combat
and rest. It will be considered their choice for that round and end the
program.

## Next Steps

Now that we have thought through some of our major components, we'll
start by writing up the characters and professions. We'll get a
chance to work with Object constructor functions and object prototypes.
We'll assign some properties to our characters and use the prototype
to give some behavior which our characters will need to battle their enemies!

{%- include contribute.html repo_link="https://github.com/skillsreactor/rpg-learning-example/releases/latest" -%}