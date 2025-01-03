---
layout: default
title: Modding Introduction
nav_order: 1
---

# Modding Introduction

In this section, we will briefly explain how modding with TSWoW looks and feels like. There are no exercises here, just read it through and try to understand the basic concepts. When finished, you're not expected to understand **how** to create these types of mods, but simply know that they exist.

## VSCodium

With TSWoW, we use the TypeScript programming language in the VSCodium editor to modify the games data and behavior. Below is how modding typically looks inside it, we have:

1. A file window where all our code files are at
2. A code window where we can edit text files
3. A terminal window where we can type in commands

<img class="mi ili" src="https://i.imgur.com/28TQrpa.png">

## Modules

TSWoW is built around the concept of _modules_. A module is a folder that is placed inside the `modules` folder in your tswow installation and is used to contain scripts and assets that modifies the game in some way.

<img class="mi ili" src="https://i.imgur.com/eianlcc.png">

## Mod Types

Modules can contain different types of _subfolders_ that are used to modify the game or the server in specific ways:

1. **addon**: used to modify or extend the clients interface files, similarly to how player AddOns work.

2. **assets**: folder that contains assets like 3D models, textures, music or world maps

3. **datascripts**: special scripts used to generate _static game data_ such as Spells, Achievements, Items and Classes.
    - For modding veterans: these scripts generate DBC and SQL data.
    - Static means that this is game data that doesn't change while the game itself is running.

4. **livescripts**: scripts that is running inside the server itself, allowing things like running code when player types in chat messages or engaging a boss.

<img class="mi ili" src="https://i.imgur.com/PZAijnG.png">

## DataScripts

DataScripts are one of the most unique features of TSWoW compared to other cores, and is a scripting language based on TypeScript used to generate game data in code.

Despite being a proper programming language, datascript often read more like configuration files, that are often very easy to fill out even by beginners thanks to extensive use of code completion.

Here is an example datascript that creates a very simple spell:

```ts
import { std } from 'wow/wotlk';

std.Items
    .create('my-mod','my-item')
    .Name.enGB.set('My Item')
    .Class.SWORD_1H.set()
    .Damage.addPhysical(1007688,1007688)
    .RequiredLevel.set(25)
    .DisplayInfo.set(1542) // just a sword model id
```

As you can see, it should be relatively straightforward what this code is doing, even if you can't fully follow the syntax just yet.

The power of datascripts is that they allow you to create content _procedurally_, and completely adapt the way you create content for your server to your own requirements.

For example, if you wanted to create not one item, but 1 million items, datascripts make this trivial:

```ts
import { std } from 'wow/wotlk';

for(let i = 0; i < 1000000; ++i)
{
    std.Items
        .create('my-mod',`my-item-${i}`)
        .Name.enGB.set(`My Item #${i}`)
        .Class.SWORD_1H.set()
        .Damage.addPhysical(1007688,1007688)
        .RequiredLevel.set(25)
        .DisplayInfo.set(1542)
}
```

TSWoW datascripts has first-class support for more types of entities in the game than any other tool in existence, including the types of entities that have traditionally been very difficult to create. For example, here is how easy it is to create a custom class using datascripts:

```ts
std.Classes
    .create('my-mod','my-class','MAGE')
    .Name.enGB.set('Necromancer')
    .Races.add(['HUMAN','NIGHTELF','ORC'])
```

This might seem effortless, but behind the scenes this script sets up for us automatically:

- About 1000 rows in various database tables
- Hundreds of lines of code into various client interface files
- A couple of raw bytes into the client binary

Yet, if we run this datascript in TSWoW, we would instantly get a new working (but barebones) class ingame:

<img class = "mi ili" src="https://i.imgur.com/ON9RlmE.png">

## Livescripts

While Datascripts are very powerful, and they can only really generate or modify static data for the game before the game has been started.

LiveScripts are the types of scripts that TSWoW uses to create specific customized behavior inside the server itself. Here is a simple livescript that sends a message to any player who says something in the games chat:

```ts
export function Main(events: TSEvents) {
    events.Player.OnSay((player,message)=>{
        player.SendBroadcastMessage(
            `You said "${message.get()}"!`);
    });
}
```

As you can see, TSWoW livescripts are relatively simple to write, and is comparable in syntax to other scripting libraries like _Eluna_. The advantages that livescripts offers compared to other scripting solutions include:

- First-class support for interacting with data generated from _datascripts_ and client _addons_.
- Much less boilerplate than internal core scripts
- Can be automatically converted into pure C++ that runs significantly faster than scripting languages.
- Very good support for code completion

## AddOns

The name `addon` in TSWoW refers to clientside scripts used to create custom UI and behavior just like player AddOns do.

Just like datascripts and livescripts, AddOns can be written in TypeScript, which is automatically converted into Lua scripts that the client understands.

## Assets

The `asset` folder is what tswow uses to bundle game assets together with the rest of a module, and automatically loads these assets into the client when it's started.

For modding veterans, the internal structure of the `assets` folder is identical to an MPQ archive, meaning that for example, the path of a custom map called `testmap` would be `modules/my_module/assets/world/maps/testmap`.

<img class="mi ili" src="https://i.imgur.com/QLXo5Qk.png">
