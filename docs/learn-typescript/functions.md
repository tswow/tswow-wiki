---
layout: default
title: Functions
nav_order: 6
---

# Lesson 6: Functions

A `function` is a piece of code that we can give a name to and, that we can then cause to run by "calling" the function by its name from other code.

## Function Declaration

Let's create a simple function that prints out a message when we call it:

```ts
function my_function()
{
    console.log("Hello World!");
}
```

If we run the above code, you'll notice that nothing happens. This is because we have just declared that this function exists, but we never call it from somehwere. The parts of this function are as follows:

`function` - This is a keyword that tells TypeScript that we want to declare a function

`my_function` - This is the **name** of the function that we will use to call it from other code.

`()` - In function declarations, these parantheses and their contents are known as _parameters_, which we will see examples of below.

`{...}` - Just like with if statements and loops, this is a piece of code that will fire whenever the function is called.

## Function Call

Lets add to the above code an actual function call, so that we can see our message printed to the output log:

```ts
// function declaration
function my_function()
{
    console.log("Hello World!");
}

// function call
my_function();
my_function();
```

Notice especially how the code inside `my_function` runs twice, since we call the function twice.

**Function Call Components**:

`my_function` - The name of the function we're calling

`()` - In function calls, these parantheses and their contents are known as _arguments_.

## Function Arguments

When we create functions, it is possible to tell TypeScript that we want to transfer some values into them into 'local' variables that are only accessible inside the function itself. These variables are called _parameters_.

```ts
function my_function(my_arg: string)
{
    console.log(my_arg);
}
```

Here, `my_arg` is a parameter of type `string`. Parameters are declared similar to variables, except that you don't type out the `let` keyword.

Calling a function with arguments is similar to calling `if` statements or `while` loops, we simply need to insert a `string` value into the parantheses:

```ts
function my_function(my_arg: string)
{
    console.log(my_arg);
}

my_function("Hello world")
my_function("Goodbye world")
```

We can also accept multiple parameters of different types to a function that its callers must respect:

```ts
function my_function(my_arg: string, my_other_arg: number)
{
    console.log(my_arg);
    console.log(my_other_arg);
}

my_function("Hello world", 1007688);
my_function("Goodbye world", 25);
```