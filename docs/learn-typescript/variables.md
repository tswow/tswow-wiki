---
layout: default
title: Variables
nav_order: 2
---

# Lesson 2: Variables

Two of the most important concepts in programming is the concept of `data` and `code`. In the previous lesson, we saw examples of both. `console.log()` is a **code**, or **instruction**, that tells the computer to write something to the sceen, and the text `"Hello World!"` is the **data** we wanted to print on the screen.

## Variables and Data

In order to keep track of data in our programs, we use something called **variables**. A variable is a special name that can be assigned a particular value. A value could be a piece of text, a number, or something completely different.

## Creating Variables

In TypeScript, we create variables by using the code `let`:

```ts
let my_variable: string = "Hello World!"
```

The above code **creates** a variable called "my_variable" and **assigns** it the string value `Hello World!`. In TypeScript, we need to specify that the value we want to store in the variable is a string by using the suffix `: string`, this is called a _type declaration_. From this point onwards, we can now use the name `my_variable` to refer to the text `Hello World!`.

Notice how there are no quotes surrounding `my_variable` in the above example. This is because we only use double quotes to refer to string data, variables are simply written out as they are named.

An important rule of programming is that we can use variable names **in place** of the data they refer to anywhere that data could've written out directly. For example, we can provide the `console.log` code with our newly created variable:

[Example 2.0: Print Variable](https://playcode.io/1456820)
```ts
let my_variable: string = "Hello World!";
console.log(my_variable);
```

Notice how that when we feed the `console.log` code with our variable name, it prints out the value **assigned** to it, and not the variable name itself.

## Changing Variables

Once we have created a variable and assigned it with a value, that doesn't mean we're stuck with this variable name being forever tied to that specific value, but we can **change** the value of a variable whenever we please.

[Example 2.1: Re-assigning Variable](https://playcode.io/1456824)
```ts
let my_variable: string = "Hello World!"
console.log(my_variable);
my_variable = "Hello TSWoW!";
console.log(my_variable);
```

There are two important things to notice here:

- Despite that we give `console.log` the same variable twice, it prints out only the value last assigned to the variable.

- The second time we assign a value to the variable, we don't include the `let` or `: string` parts, because those are only used when we **first** create a variable to let the program know that a variable of this name exists, and that we should expect it to contain a `string` value.

## Variable Types

In the above examples, we have dealt with the `string` type, that contains text data.

Another important type in TypeScript is the `number` type, which can contain integer or decimal numbers:

```ts
let my_integer: number = 1007688;
let my_decimal: number = 25.0;
```

Numbers can also be printed to the screen using `console.log`:

[Example #2.2: Print Number](https://playcode.io/1456919)
```ts
let my_integer: number = 1007688;
console.log(my_integer)
```

However, in TypeScript, it is illegal to create a number and then try to assign it a string. The following is **not** valid code:

[Example #2.3: Type error](https://playcode.io/1456922)
```ts
let my_integer: number = "Hello World"; // does not work
```

In the playground link above, notice how the name `my_integer` has a red squiggly line under it. This is because of the type error that comes right after it.