---
layout: default
title: Livescript Objects
nav_index: 6
---

# Livescript Objects

In this tutorial, we will learn how to create a new command that expands on the previous one, that sent us back a message when we sent it, to instead send a message to the player we currently have selected in the game, _if we have any player selected_.

This will require us to learn a few important concepts about **types** and **validity checking**, since players can both select non-player targets and might not even have a target selected at all.

## Getting a players current selection

We will start by presenting a naive extension of our previous script that attempts to send a message to our current selection. The function we call to get a players selection is `.GetSelection()`, so we might end up with the following code.

<img class="mi ili" src="https://i.imgur.com/No5KiNg.png">

As you can see, this causes VSCodium to complain that this code is not valid. This is becase we haven't made sure that the players target is really a player yet, and to know how to do that we need to learn a little about _Type Hierarchies_.

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

In order to send a message to the player selection, we need to _cast_ it into a player. This is done with a special method that all Objects have called `ToPlayer`, which returns a Player type that we can call the method on. We get the following modified code.

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            player.GetSelection().ToPlayer().SendBroadcastMessage(` selection pong!`);
            found.set(true);
        }
    })
}
```

This code will actually compile and even work if we try it ingame with a player selected.

<img class="mi ili" src="https://i.imgur.com/MmmB7KL.png">

However, if we have a **Creature** selected, or nothing at all, we'll get an unpleasant surprise:

<img class="mi ili" src="https://i.imgur.com/cEgWnO5.png">

<img class="mi ili" src="https://i.imgur.com/FnoRumm.png">

## Validity Checking

When we access entities from uncertain sources or when we aren't sure what exact type they are, we need to check that they are actually valid after we do so.

To do this, we need to save them into a variable, and simply try out their `IsNull` method. If that check passes, we know that the entity we have is valid, and we can write further code to handle the cases where it wasn't:

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            let selection = player.GetSelection().ToPlayer();
            if(!selection.IsNull())
            {
                selection.SendBroadcastMessage(`selection pong!`);
            }
            else
            {
                player.SendBroadcastMessage(`You're not selecting a player!`)
            }
            found.set(true);
        }
    })
}
```

This is much better, and we now have a secure script that won't crash the server regardless of what we try to throw at it.

<img class="mi ili" src="https://i.imgur.com/yQLBe18.png">
<img class="mi ili" src="https://i.imgur.com/edUvc3F.png">