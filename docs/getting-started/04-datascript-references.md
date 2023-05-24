---
layout: default
title: Datascripts References
nav_index: 4
---

# Datascript References

Many entities in World of Warcraft reference other entities, or contain lists of entities.

For many of these references, TSWoW offers first-class support to modify, create and traverse them through some common conventions that datascript developers should be aware of.

In this tutorial, we will use this as an opportunity for learning how to create `Spells`, which is one of the most important entities in the game to understand.

## Spells

Before we start, we need to understand a little about how Spells work internally in the game to make sense of the references it uses.

Spells are themselves defined in a DBC file called `Spells.dbc`. This file contains fields that define various attributes for the spell and what it _does_.

### Spell Effects: Entity Lists

What a spell _does_ is called a **spell effect**. Every spell in the game holds in it up to three spell effects, which are all defined alongside the spell itself in `Spell.dbc`. Spell effects can do things like:

- Deal damage to an enemy
- Apply an aura for 10 seconds that deals damage every 2 seconds
- Apply a permanent aura that increases all stats by 10%

Let's look at `Fireball` as an example. It consists of two effects:

1. Dealing immediate fire damage
2. Dealing periodic fire damage over 8 seconds

<img class="mi ili" src="https://i.imgur.com/brAu677.png">

While spell effects are separate entities from Spells themselves, they are _local_ to the spell, and cannot be referenced by multiple spells at the same time.

### Spell Visuals: Entity References

The Spell itself also does not define much about how it looks, referred to as the spells `Visuals`. Spell Visuals are instead themselves separate entities that are just _referenced_ by the spell ID, which is commonly used to define the same visuals for spells of different ranks.

<img class="mi ili" src="https://i.imgur.com/cU7Hfov.png">

To complicate things further, spell visuals consists of various components to define the spells visuals at different stages, called `spell visual kits`. These spell visual kits are themselves separate entities that _also be referenced by multiple spell visuals_.

Fireball visuals use 3 spell visual kits:

- PrecastKit: Defines visuals and animations for the **caster** when the spell begins casting and for as long as the castbar is visible
- CastKit: Defines visuals and animations for the **caster** when the castbar finishes
- ImpactKit: Defines visuals and animations for the **target** of the spell when it hits them.

<img class="mi ili" src="https://i.imgur.com/JV9qpy8.png">

## Creating a Fireball-like spell

For the rest of this tutorial, our goal will be to create a variation on the normal Fireball spell by cloning it and applying some changes. The changes we will make are:

- Change the damage value of the immediate damage effect
- Apply a debuff on the target that slows them down
- Change the casting animation to one that looks more like a healing spell

This won't create a very aesthetically pleasing or interesting spell, but it will explain the most important concepts that we are trying to illustrate.

### Finding the Parent ID

Our first step is to figure out the ID of a normal fireball spell to use as a basis. We could do this either via [datascript queries](./03-datasript-queries) or by creating a mage character and inspecting the fireball spell we learn on creation. As we do, we'll find that the ID we're looking for is `133`.

<img class="mi ili" src="https://i.imgur.com/KfC5bqs.png">

### Basics

By using the parent ID, we can easily create our own functional fireball spell using just a couple of lines of code.

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')
```

<img class = "mi ili" src="https://i.imgur.com/j9hAbd9.png">

### Changing Damage Values

The effects of a spell can be accessed through the `Effects` property, and we can print out the values of this property just like we can with entities themselves. This will be useful to figure out which effect in the spell is doing what.

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')

// we use objectifyPlain here because the output
// is easier to understand for beginners
console.log(MY_SPELL.Effects.objectifyPlain())
```

Running this code shows us clearly what effects does what in this spell:

- The **first** effect deals immediate damage
- The **second** effect deals periodic damage
- The **third** effect is unused

```ts
[
  {
    ItemType: 0,
    Aura: 'NONE',
    Type: 'SCHOOL_DAMAGE',
    Mechanic: 'NONE',
    PointsBase: { effective: 14, stored: 13 },
    PointsDieSides: 9,
    PointsPerLevel: 0.6000000238418579,
    PointsPerCombo: 0,
    ImplicitTargetA: 'UNIT_TARGET_ENEMY',
    ImplicitTargetB: 0,
    AuraPeriod: 0,
    MultipleValue: 0,
    ChainTarget: 0,
    MiscValueA: 0,
    MiscValueB: 0,
    TriggerSpell: 0,
    ChainAmplitude: 1,
    BonusMultiplier: 0.12300000339746475,
    ClassMask: []
  },
  {
    ItemType: 0,
    Aura: 'PERIODIC_DAMAGE',
    Type: 'APPLY_AURA',
    Mechanic: 'NONE',
    PointsBase: { effective: 1, stored: 0 },
    PointsDieSides: 1,
    PointsPerLevel: 0,
    PointsPerCombo: 0,
    ImplicitTargetA: 'UNIT_TARGET_ENEMY',
    ImplicitTargetB: 0,
    AuraPeriod: 2000,
    MultipleValue: 0,
    ChainTarget: 0,
    MiscValueA: 0,
    MiscValueB: 0,
    TriggerSpell: 0,
    ChainAmplitude: 1,
    BonusMultiplier: 0,
    ClassMask: []
  }
]
```

To modify an existing effect on the spell, we can use the `Effect.mod` method, which accepts an effect index, and then a _callback function_ that will allow us to modify the entity inside of.

If you don't know what a callback function is, it is enough to just follow the syntax and it should hopefully make intuitive sense what is going on.

The effect field we want to edit to change damage values is `PointsBase`, and is done like this:

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')
    .Effects.mod(0,x=>x
        .PointsBase.set(1007688)
    )
```

Everything inside of the parantheses starting from `mod(` affects the effect, while anything after the last `)` again operates on the spell object itself.

If we try out this spell in-game we should see that it now deals a ridiculous amount of damage.

<img class="mi ili" src="https://i.imgur.com/rV9zmdH.png">

### Adding new effects

Adding new effects is done almost the same way as modifying existing ones, but instead we use the `Effects.addMod`, which allows us to yet again supply a callback function to modify the new effect.

For this effect, we need to set up a `Type` and an `Aura` on our effect to slow down the enemy.

- The `APPLY_AURA` type sets up the effect to use the `Aura` field
- The `MOD_DECREASE_SPEED` aura type sets up the effect to accept percentage values
- To see the effect in action, i also reduce the earlier effects damage to 1 so the target don't just die immediately.

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')
    .Effects.mod(0,x=>x
        .PointsBase.set(1)
    )
    .Effects.addMod(x=>x
        .Type.APPLY_AURA.set()
        .Aura.MOD_DECREASE_SPEED.set()
        .PercentBase.set(-90)
    )
```

If we check the spell ingame, we should see that the target creature can barely walk towards us while the effect is active

<img class="mi ili" src="https://i.imgur.com/w63reNT.png">

### Modifying Visuals

Spell effects are relatively straightforward to work with, we can either `mod` or `addMod` them, and if we try to add too many effects the script will print out an error.

Visuals are slightly more complex to work with because they are shared entities that multiple spells can reference, and we have a few more methods to work with.

Visuals are stored on the spells `Visual` property, and has a few methods we should be aware of:

- `Visual.set`: Accepts a numeric ID that identifies a `SpellVisual` object.

- `Visual.modRef`: Accepts a callback function and allow us to modify the spell visual object similarly to how we modified spell effects before.

- `Visual.modRefCopy`: Looks identical to Visual.modRef, but has a very important difference. Before accessing, this method creates a **copy** of the underlying visual object before allowing us to access it. This allow us to change the visual style of our new spell **without changing the style of other spells** that might use the same visual as well.

Since we're only trying to create a test spell to understand spells better, we will use the `Visual.modRefCopy` method

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')
    .Effects.mod(0,x=>x
        .PointsBase.set(1)
    )
    .Effects.addMod(x=>x
        .Type.APPLY_AURA.set()
        .Aura.MOD_DECREASE_SPEED.set()
        .PercentBase.set(-42)
    )
    .Visual.modRefCopy(x=>x
    )
```

### Spell Visual Animations

Spell visual animations are stored on the SpellVisualKits, and for the kit used while the castbar is up is the `PrecastKit`. Since spell kits are individual entities just like SpellVisuals themselves, we are presented with the same set of methods when we access these kits, and we yet again want to use the `PrecastKit.modRefCopy` function to ensure we don't mess with any existing spell visuals:

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')
    .Effects.mod(0,x=>x
        .PointsBase.set(1)
    )
    .Effects.addMod(x=>x
        .Type.APPLY_AURA.set()
        .Aura.MOD_DECREASE_SPEED.set()
        .PercentBase.set(-42)
    )
    .Visual.modRefCopy(x=>x
        .PrecastKit.modRefCopy(y=>y
            // Nested references!
        )
    )
```

The field we now want to edit on this visual kit is the `Animation` field, and we can choose any animation we want for some funky and strange effects, but i'll use the `SPELL_CAST_OMNI` effect to not get too funky.

```ts
import { std } from "wow/wotlk";

const MY_SPELL = std.Spells
    .create('my-mod','my-fireball',133)
    .Name.enGB.set('My Custom Fireball')
    .Effects.mod(0,x=>x
        .PointsBase.set(1)
    )
    .Effects.addMod(x=>x
        .Type.APPLY_AURA.set()
        .Aura.MOD_DECREASE_SPEED.set()
        .PercentBase.set(-42)
    )
    .Visual.modRefCopy(x=>x
        .PrecastKit.modRefCopy(y=>y
            .Animation.SPELL_CAST_OMNI.set()
        )
    )
```

If we run this code, we should be able to see that our character is now using a different animation while charging up the spell:

<img class="mi ili" src="https://i.imgur.com/K63rJo0.png">

## Summary

In this section, we have learnt:

- How to create and modify simple spell effects
- How to modify spellcast animations
- What references are and how to set them up
- The difference between local and shared entities
- Why we want to create copies of shared entities before modifying them