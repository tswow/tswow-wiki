---
layout: default
title: Datascripts Basics
nav_order: 2
---

# Datascripts Basics

This section will teach you how to write a very simple datascript to create a basic weapon in the game.

It is assumed that you have already [installed tswow](../install/repack) along with VSCodium and know how to start the program.

## Starting TSWoW

Before we start, we should make sure that we have started TSWoW. Fire up your VSCodium, and point it at your TSWoW installation directory. You can select what folder VSCodium currently has open by pressing `F1`, typing in `Open Folder` and then select the first option that comes up.

<img class="mi ili" src="https://i.imgur.com/zC3MtnQ.png">

This should open up a view that looks like this:

<img class="mi ili" src="https://i.imgur.com/wYsVJoh.png">

If your view doesn't already have a terminal window open at the bottom, we also want to type in the command `Toggle Terminal` to make it appear.

<img class="mi ili" src="https://i.imgur.com/9wuYOXf.png">

With the terminal window open, starting TSWoW is as simple as typing in `./start`

<img class="mi ili" src="https://i.imgur.com/UQelPKa.png">

If you haven't started TSWoW before, it might take it a while to build all the initial data, but once done you should have a view that looks similar to this

## Creating a module

First, we will need to create a new module to place our datascripts inside.

This is as simple as opening up the `modules` folder in our tswow installation directory and create another folder with the name of our module.

- Spaces are not allowed in your module name
- Avoid special non-ascii characters in your module name
- Clean tswow installations might only include the `default` module, but it is advisable to not use this module for our own code.

<img class="mi ili" src="https://i.imgur.com/UN2hMd4.png">

Inside of this new module, we will need to create another directory called `datascripts`.

<img class="mi ili" src="https://i.imgur.com/tA4hTKt.png">

If we have TSWoW running and open this folder, we should find that somehow there are already a bunch of files in here. This is because TSWoW detected that we created a new datascript folder and populated it with the basic boilerplate necessary for us.

<img class="mi ili" src="https://i.imgur.com/hvYLgHw.png">

We can also view these files inside VSCodiums own file outline, which is recommended to use for navigating files inside the editor.

<img class="mi ili" src="https://i.imgur.com/SplOHUR.png">

## Building DataScripts

The file in here that we are interested in is `datascripts.ts`, and if we open it we can see that TSWoW has already filled out a basic print statement in it for us.

<img class="mi ili" src="https://i.imgur.com/j8VP8J6.png">

To build datascripts, we type in the command `build data` into the tswow console.

- Since we are just currently building a print statement, we can use the variation `build data --readonly`, which will just run the scripts without actually changing anything or restarting the client/server.
- Readonly builds are good for when you only want to inspect print output

<img class="mi ili" src="https://i.imgur.com/QlM0B5K.png">

This should start the build process and eventually give us the following output in the terminal:

<img class="mi ili" src="https://i.imgur.com/lj1Ew3P.png">

## Creating an Item

Most types of entities you can create with datascripts come through the `std` interface that tswow provides. Before we create our item, we should make sure that the `std` module is imported into our script. This is done by adding the statement `import { std } from "wow/wotlk";` (in the generated script, this should already be included):

```ts
import { std } from "wow/wotlk";

console.log("Hello from my-module data script!");
```

The `std` module exposes a **lot** of entity types, and accessing them once the std module is imported is as simple as typing `std.EntityType` and let the autocompletion guide you from there. Since we are creating an item, we want to use `std.Item.create`.

As we type this in, we will see that the autocompletion asks us to provide a "mod" name and an "id":

<img class="mi ili" src="https://i.imgur.com/nNmfeCN.png">

### Mod/Id pairs

Before we move on, we will need to understand how IDs work in World of Warcraft.

More or less all types of entities in the game, from _items_  to _creatures_, _spells_, _quests_  and _classes_, have a **numerical id** tied to them.

For example

- The spell `Fireball` has the spell ID `133`
- The item `Thunderfury, Blessed Blade of the Windseeker` has the item id `19019`
- The class `Priest` has the class id `5`.

Ids are typically unique to the type of entity that it describes, meaning that, for example, **spell id** 25 is a completely different thing from **item id** 25.

Numerical ids make a lot of modding difficult, because we always have to remember what ids we have used to make sure that they don't collide with each others. Additionally, many of the internal client and server tables prefer IDs to be as _tightly packed_  as possible, which can be very hard to figure out manually in your head.

TSWoW solves part of this problem by using **text identifiers** that _generate_ numerical ids that are used internally by the server, in a way that modders never have to worry about what numerical ids their entities use.

String identifiers are split into two parts:
- the `mod` identifier, which is often just the name of the current module
- the `id` identifier, which should be a descriptive and **unique** name for the entity. This is not the same as the entitys 'display name'

The mod part is used to make sure that entities in different modules don't collide with each others. For example, if two modules had an entity named `sword`, the modules different `mod` identifiers will make sure the game understands that these are two different items.

### Finishing our Item

Now that we know how mod/id pairs work, we can give our item our mod id and then a descriptive name:

```ts
import { std } from "wow/wotlk";

std.Items.create('my-module','my-sword')
```

This code would, on its own, be enough to create a (very) barebones item in the game, and we will need to apply some _properties_ to it to actually turn it into a sword.

In TSWoW, we do this by typing the `.` character under an entity we have created, which should prompt the autocompletion to provide us with a list of properties we can set:

<img class="mi ili" src="https://i.imgur.com/W6mKSva.png">

Let's set the items english display name to `My Sword`, by setting the `Name.enGB` property:

```ts
import { std } from "wow/wotlk";

std.Items.create('my-module','my-sword')
    .Name.enGB.set('My Sword')
```

Once we have set one property on our entity, setting a new one is as simple as starting a new line and typing in `.` again, which should bring up the same menu as before

<img class="mi ili" src="https://i.imgur.com/2P8amlR.png">

Let's change our item type to be a one-handed sword, this is done by changing its `Class` property:

```ts
std.Items.create('my-module','my-sword')
    .Name.enGB.set('My Sword')
    .Class.SWORD_1H.set()
```

The `Class` property is known as an `enum` type, meaning it has a set of predetermined valid values. By typing `Class.SWORD_1H.set`, we tell TSWoW that we want to set the items class to `SWORD_1H`.

We will also need to define how our item is equipped, as the game doesn't actually derive that from its item type, silly enough. This is easily done by setting its `InventoryType` to `WEAPON`:

```ts
std.Items.create('my-module','my-sword')
    .Name.enGB.set('My Sword')
    .Class.SWORD_1H.set()
    .InventoryType.WEAPON.set()
```

For our item to show up correctly in the game, we should also tie it to a display id. For this, we will use the id  `1542`, which is a common id used by swords in the game. Creating these types of displays from scratch is out of scope for this tutorial, but in a future tutorial we will learn how to go about finding these ids.

```ts
import { std } from "wow/wotlk";

std.Items.create('my-module','my-sword')
    .Name.enGB.set('My Sword')
    .Class.SWORD_1H.set()
    .InventoryType.WEAPON.set()
    .DisplayInfo.set(1542)
```

This code is sufficient to create a very simple sword item, and to put it into the game all we now need to do is run `build data`. This will shut down the server and client, run all our scripts, and then restart them again.

If we log back into the game, we should be able to find our item by searching for its display name (not its id) "My Sword":

<img class="mi ili" src="https://i.imgur.com/AMJB4Sd.png">

For me, the id of this item became 60000, and I can add it to my character with the command `.additem 60000`. If your id is different, replace this with the id on your screen. We should receive our newly created weapon that we can equip just like any sword.

<img class="mi ili" src="https://i.imgur.com/ONd9xn6.png">

## Summary

In this tutorial we have learnt:

- How to create a module to organize our edits
- How to add datascripts to our module
- How to build datascripts
- How to use datascripts to create a simple item

It is recommended that you now try and play around a little with the item we have created. For example, you may want to try adding some `Damage` or `Stats` to it.