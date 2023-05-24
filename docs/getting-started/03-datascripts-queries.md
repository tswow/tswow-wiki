---
layout: default
title: Datascripts Queries
nav_index: 3
---

# Datascript Queries

In this section, we will learn how we can use datascripts to find information about our own or existing entities in the game. We will also learn how to use TSWoWs builtin AddOns to easily find the numerical IDs of various entities in the game.

## Ingame ID Viewer

TSWoW ships with an improved version of the ID viewer AddOns that players sometimes use to see item and spell ids. For example, if we hover an item in our inventory inside the game, we should see that we can see the items `ItemID` and `DisplayID` in addition to the normal tooltip. The same also works for **Spells** and **Creatures**.

<img class="mi ili" src="https://i.imgur.com/GXLmBvD.png">
<img class="mi ili" src="https://i.imgur.com/YbqlsiC.png">
<img class="mi ili" src="https://i.imgur.com/SxgeLPM.png">

This feature is very useful when working with various entities in the game, as the client itself serves as a pretty good live viewer for these kinds of things.

## Entity Loading

We can use datascripts not only to create new entities, but also load or even modify the ones that are already in the game. Let's load our hearthstone item up in a datascript and save it into a variable.

```ts
import { std } from "wow/wotlk";

const HEARTHSTONE = std.Items.load(6948)
```

On its own, this doesn't do very much, but we can now read or modify the `HEARTHSTONE` variable just as if we had done with our own item in the previous tutorial.

What we can also do is ask our datascript to print out a bunch of information about this item when we build it, which is very useful to learn how different entities in the game works. This is done by using the `objectify` function that exists on almost all TSWoW entities, and we can feed that output into a normal log call:

```ts
import { std } from "wow/wotlk";

const HEARTHSTONE = std.Items.load(6948)

console.log(HEARTHSTONE.objectify());
```

If we now build our datascripts with the `build data --readonly` flag, we should get a very long list of output that contains all we could possibly want to know about this item.

<img class="mi ili" src="https://i.imgur.com/iXhXqy2.png">

`objectify` is a very powerful tool when working with TSWoW to rapidly learn how different entities work in the game when we want to replicate their behavior. If we just have a subset of things we want to learn about an item, we can also access its individual propertes and `.get` them and instead print out the value of that:

<img class="mi ili" src="https://i.imgur.com/qBfjVOm.png">

## Entity Parenting

If we do not want to have to write all our entities from scratch, it is generally possible to use other entities in the game as **parents** for our own, meaning that all its fields and properties are copied over on creation. Creating items based on other items is as simple as providing a parent numerical id after our _mod/id_ pair:

```ts
import { std } from "wow/wotlk";

std.Items
    .create('my-mod','my-cloned-item',6948)
    .Name.enGB.set('My New Hearthstone')
```

If we now log into the game, we should have a new item that is functionally the same as a normal hearthstone but with a new name:

<img class="mi ili" src="https://i.imgur.com/KMBhg8L.png">

## Queries

We can also use TSWoW to _query_ for entities based on some of its properties other than the ID itself. This is done using either the `find`/`filter` or `query/QueryAll` functions.

### find/Filter

These functions will iterate over every single entity of a given type in the game, and allow us to supply a _callback_ function to check if it's what we're looking for, just like how the normal javascript `find/filter` methods work.

- `find` will search until the first entity it finds and return that entity, or returns `undefined` if it doesn't find any entity at all.

- `filter` will continue to search and return a list of **all** entities that match the callback.


**Using find:**
```ts
import { std } from "wow/wotlk";

let hearthstone = std.Items.find(item=>item.Name.enGB.get() == 'Hearthstone')
if(item)
{
    console.log('Found hearthstone with id ' + hearthstone.ID)
}
```

**Using filter:**
```ts
import { std } from "wow/wotlk";

let hearthstones = std.Items.filter(item=>item.Name.enGB.get() == 'Hearthstone')
for(let hearthstone of hearthstones)
{
    console.log('Found hearthstone with id ' + hearthstone.ID)
}
```

### Advanced: query/queryAll

One of the drawbacks of using find/filter is that it's a relatively slow operation, especially for entities that are internally stored in `sql` tables, as it forces TSWoW to load all of them into memory.

The query/queryAll methods are an alternative way that translates directly into a query language understood by the underlying storage engine, but keeping the type safety that TypeScript offers. The drawback of this method is that the fields we can set on these query objects are not always the same as the ones available on the entity class itself, because we are limied to the fields that exists on the main row that defines the entity type.

The `query/queryAll` functions accepts _query objects_, which looks similar to the way `mongodb` queries work:

```ts
import { std } from "wow/wotlk";

let item = std.Items.query({name: "Hearthstone"})
if(item)
{
    console.log('Found hearthstone with id ' + item.ID)
}
```

## Comparing Entities

Datascripts offers an easy way to compare objects to see how they differ. This is a very useful tool for debugging issues when creating entities.

```ts
import { std } from "wow/wotlk";

let hearthstone = std.Items
    .query({name:'Hearthstone'})

let new_hearthstone = std.Items
    .create('my-mod','new-hearthstone',hearthstone.ID)
    .Name.enGB.set('My New Hearthstone')

let diff = std.Compare(
    hearthstone.objectify(),
    new_hearthstone.objectify()
)

console.log(diff)
```

This should produce the following output:

```ts
{
  Name: {
    enGB: { left: 'Hearthstone', right: 'My New Hearthstone' },
  }
}
```