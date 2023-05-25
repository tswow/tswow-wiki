---
layout: default
title: Loops
nav_order: 5
---

# Lesson 5: Loops

Loops are a more advanced form of control flow that allow us to tell the program to keep doing something until some boolean value no longer holds.

There are two basic loop types that every programmer should know, the numeric `for` loop and the `while` loop.

## The `while` loop

The simplest type of loop is the while loop. It looks very similar to an if-statement, but differs in that it **repeats** its body for as long as the boolean value in its parantheses hold:

```ts
let my_number: number = 0;
while(my_number < 5)
{
    console.log("While Loop firing!")
    my_number = my_number + 1;
}
```

While loops can be dangerous, and it's very easy to accidentally write code that causes **infinite loops**. For example, if we forget to increase our number inside the loop body above, the boolean expression will never be false and will never stop printing (until the program crashes or is shut down):

```ts
let my_number: number = 0;
while(my_number < 5)
{
    console.log("While Loop firing!")
    // my_number = my_number + 1;
}
```

## The `for` loop

A slightly better loop to use when counting things is the `for` loop. This is a slightly more complex type of loop, but it allows us to do all the counting inside of the first parantheses instead of the loop body, making it harder to do the kind of mistake we showed above.

```ts
for(let my_number = 0; my_number < 5; my_number = my_number + 1)
{
    console.log("For loop firing!");
}
```

This loop uses a very special format inside its parentheses and consists of 3 statements separated by semicolons `;`

- `let my_number = 0` - This part allows us to choose what number the loop should start from.

- `my_number < 5` - This part allows us to choose what boolean expression we should check to see if the loop should continue firing

- `my_number = my_number + 1` - This part allows us to choose how the number is supposed to change between each iteration of the loop.

Aside from this, the loop body works just like a `while` loop or `if` statement.