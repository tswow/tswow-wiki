---
layout: default
title: Livescript Objects
nav_order: 6
---

# Livescript Objects

In this tutorial, we will learn how to create a new command that expands on the previous one. Instead of just sending ourselves a message, we'll the command cast a spell on our currently selected target.

## Getting a players current selection

We will start by presenting a naive version of this script. The function we call to get a players selection is `.GetSelection()`, so we might end up with the following code:

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            // casts "Fireball" on our current selection
            player.CastSpell(player.GetSelection(), 133)
        }
    })
}
```

However, if we type this code into vscode it'll show us an error with a red line under the `player.GetSelection()` expression.

<img class="mi ili" src="https://i.imgur.com/WShf9aE.png">

The reason for this is that players don't always have selections, and if we check the return value of `GetSelection()`, we'll see that it can be undefined.

To fix this, we can use normal typescript conventions for dealing with undefined values by checking it with an if statement:

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            const selection = player.GetSelection()
            if (selection)
            {
                // casts "Fireball" on our current selection
                player.CastSpell(selection, 133)
            }
        }
    })
}
```

## Sending a Message

To expand the above example, we'll also try sending a chat message to the the target before casting the spell on them. We might end up with the following code:

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            const selection = player.GetSelection()
            if (selection)
            {
                // casts "Fireball" on our current selection
                selection.SendBroadcastMessage(`Fireball!`)
                player.CastSpell(selection, 133)
            }
        }
    })
}
```

However, even though we already checked the selection, vscode complains again about the `selection` variable. Why is this?

<img class="mi ili" src="https://i.imgur.com/IptUOh0.png">

## Type Hierarchies

We have already learnt about the `Player` object type in the previous tutorial, where we could send them a chat message using the `player.SendBroadcastMessage` method.

Players are not the only types of entities that exist in the running server, but they exist in a **type hierarchy** along with other types of entities, such as `Creatures` (non-player characters) and `GameObjects` (chairs, chests, doors, flagpoles etc.).

A **type hierarchy** is a concept from object-oriented programming, and means that multiple object types can have common ancestors that they inherit 'methods', or functions from. The `SendBroadcastMessage` function we call on player objects with the `.` operator is one such _method_, and _method_ in this context just means "function that is attached to an object".

It is not necessary to understand all the aspects of type hierarchies to use livescripts, but it is useful to know what it means for what we can do with entities in the game.

The root object types used by all 'object' entities in the game is the `Object` type, and it has (for our purposes) the following important child types:

- `Object`
    - `WorldObject`: Anything that can be spawned in the world
        - `GameObject`: Doors, chests, buttons, chairs etc
        - `Unit`: Anything that can walk around and enter combat
            - `Player`: Player characters logged into the game
            - `Creature`: Any unit that is not a player

What it means for a type to be a child type of other types is that they always get all the same methods as the parent. For example, the `Unit` type has a `GetName` method, which means that all players and creatures also has a `GetName` method.

A more intuitive way to say this can be that "All Players are Units, but all Units are not Players, because some are Creatures instead".

## Downcasting

So we understand that `Units` can be both `Players` and `Creatures`, and it turns out that the type value of `player.GetSelection()` is a `Unit`. Since `Units` are not always `Players` and it doesn't make any sense to send chat messages to creatures, it doesn't have any `SendBroadcastMessage` method.

In order to send a message to the player selection, we need to _cast_ it into a player. This is done with a special function called `ToPlayer`, which returns a Player type that we can call the method on. We get the following modified code.

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            const selection = ToPlayer(player.GetSelection())
            if (selection)
            {
                // casts "Fireball" on our current selection
                selection.SendBroadcastMessage(`Fireball!`)
                player.CastSpell(selection, 133)
            }
        }
    })
}
```

Because a downcast like `ToPlayer` might fail, the selection could be a creature, we need to do that cast _before_ we check the variable in the `if`-statement, as the `ToPlayer` function will return an `undefined` object if the input is `undefined` _or_ if the input is not a player.