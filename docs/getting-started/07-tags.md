---
layout: default
title: Tags
nav_index: 7
---

# Tags

While livescripts and datascripts cannot communicate directly, sometimes it is useful to be able to reference entities created in datascripts from livescripts, which is especially useful for attaching events to specific entities.

In this tutorial, we will learn how to attach a livescript to the spell we created in the earlier [datascript tutorial about spells](./04-datascript-references).

## ID-bound Livescript Events

The livescript event we created in the previous tutorial was _unbound_, and fires for every single chat command entered into the game.

Some livescript categories accept two arguments to its event handlers, where the first argument is a **numeric id** or **array of numeric ids** that the event should be _bound_ to.

For example, `Spell` events all can all either accept one or two arguments into its events, where if it receives two arguments expects the first to be a number/list of numbers corresponding to spell ids that the event should be bound to.

If we wanted to attach an event to the builtin Fireball spell, we could use code similar to this:

```ts
export function Main(events: TSEvents) {
    events.Spell.OnCast(133,(spell)=>{
        let player = spell.GetCaster().ToPlayer();
        if(player)
        {
            player.SendBroadcastMessage(`Hello from fireball!`)
        }
    })
}
```

But as we learnt before, entities we create in datascript don't use numeric ids and we want to avoid them as much as possible, so how can we get _custom_ ids to work in livescripts?

## Datascript Tags

In TSWoW, we use a _tag_ system to propagate entity ids from datascripts and into livescripts.

If we look at the code we created for the spell in the previous tutorial, we can see that it has a field called `Tags`

<img class="mi ili" src="https://i.imgur.com/d5TR4qD.png">

This field itself has two important methods:

- **add**: Allows us to attach a non-unique tag to our spell
- **addUnique**: Allows us to attach a unique tag to our spell

For the purpose of this tutorial it doesn't matter too much which one we use, but for simplicity we will use `add`, which will allow us to attach more spells to this tag in the future.

Tags use a similar format to entity ids with `mod`/`id` pairs, but have an important difference; they are **not** unique for each type of table. If a spell and an item uses the same mod/id pair in tags, those two ids will collide.

The reason for this is that the tag system is a much more _generic_ system than the id system, and can be used for almost any purpose to send numeric data between datascripts and livescripts.

By attaching a tag to our datascript spell, we should end up with the following code:

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
    // this is the code we add
    .Tags.add('my-mod','custom-fireball-scripts')
```

Once we've done this, it's important that we `build data` once to let the build system know about the tag we just created.

## Livescript Tags

Once we've created a tag in our Datascript, we can get its value inside a livescript by using one of two global functions:

- `TAG(mod,id)`: Used for non-unique tags (matching `Tags.add`)
- `UTAG(mod,id)`: Used for unique tags (matching `Tags.addUnique`)

Putting all of this together, we can create a new livescript that hooks our own custom spell instead of the builtin fireball spell:

```ts
export function Main(events: TSEvents) {
    events.Spell.OnCast(TAG("my-mod","custom-fireball-scripts")
        , (spell)=>{
            let player = spell.GetCaster().ToPlayer();
            if(!player.IsNull())
            {
                player.SendBroadcastMessage(`Hello from custom fireball!`)
            }
        })
}
```

<img class="mi ili" src="https://i.imgur.com/YPPGpB2.png">