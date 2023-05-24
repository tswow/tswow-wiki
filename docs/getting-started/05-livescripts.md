---
layout: default
title: LiveScripts
nav_index: 5
---

# LiveScripts

Livescripts are the type of scripts that run inside of the server while it's running. Just like with Datascripts, the language we typically use is TypeScript, but it is also possible to write LiveScripts using C++ or Lua. In this tutorial we will use TypeScript, which will be automatically converted into Lua by the compiler.

In this tutorial, we will learn how to create a simple chat command called `.ping` that will print out a message to the calling player just to get started.

We will cover the following concepts:

- Livescript events
- Event callbacks
- Player objects
- Mutable references

## LiveScripts and DataScripts

It is important to understand that while Livescripts and datascripts are both written using TypeScript, they fundamentally work **very differently**.

It is not possible to use code written in datascripts inside livescripts and vice versa, as these languages run in fundamentally different environments.

## Setting up Livescripts

Enabling livescripts for our module is as easy as it was for datascripts, we'll simply create a folder inside our module called "livescripts".

<img class="mi ili" src="https://i.imgur.com/MIyYzoK.png">

Just like when we created the datascripts folder, if we have TSWoW running we should notice that it instantly populates this folder with the basic boilerplate we need.

The file we are interested in here is `livescripts.ts`:

```ts
export function Main(events: TSEvents) {
    // Register your events here!
}
```

As opposed to datascripts, livescripts uses a _single entrypoint_ and do not automatically load all code in all files. This function, called `Main`, is that function.

The `Main` function accepts a single argument called `events`, which is how we can interact with the server through our scripts. The `events` struct itself contains tons of categories for different events we can subscribe to.

## Chat command

To create a chat command, we use the `events.Player.OnCommand` event, which accepts a single callback function with 3 parameters:

1. **player**: The player object that wrote the command
2. **command**: A **mutable reference** to the command string
3. **found**: A **mutable reference** to a boolean for if we want to tell the underlying system that we did something with this command in our handler.

A **mutable reference** is a type of variable that we can change the value of, which will reflect changes in the calling functions. We will see further down how to access and modify these mutable references.

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{

    })
}
```

Currently, our command handler doesn't do anything, so let's change that.

The first thing we'll need to do is check if the entered chat string is what we're looking for, which is the string value `"ping"` (command handlers do not count the initial `.` that players type in)

This is done by accessing the mutable reference `command` with the `.get` method:

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {

        }
    })
}
```

To send a message back to the player, we will use the method `SendBroadcastMessage`. Despite its name, this method only sends a message to the player themselves.

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            player.SendBroadcastMessage(`pong!`)
        }
    })
}
```

Finally, if the command matched what we expected, we want to inform the underlying command handler system that we have successfully handled the command. This is done by using the `set` method on the `found` parameter to make it `true`. Note that we only want to do this if the command was actually what we were looking for, as these handlers run for **every** command in the game.

```ts
export function Main(events: TSEvents) {
    events.Player.OnCommand((player,command,found)=>{
        if(command.get() == "ping")
        {
            player.SendBroadcastMessage(`pong!`)
            found.set(true);
        }
    })
}
```

This is all the code we will need to get our simple command to work in game, and we can insert it into the server with the command `build scripts`.

As opposed to datascripts, livescripts can be swapped into a running server without restarting, so if we already had the game open we can try it out immediately once the scripts have finished compiling.

<img class="mi ili" src="https://i.imgur.com/DY86xAy.png">

<img class="mi ili" src="https://i.imgur.com/7qQ8XO2.png">

<img class="mi ili" src="https://i.imgur.com/FnloNlx.png">