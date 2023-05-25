---
layout: default
title: Control Flow
nav_order: 4
---

# Lesson 4: Control Flow

Logic is at the core of almost all programming. Control flow refers to how we cause the program to behave in different ways depending on some `truth value`.

In TypeScript, and most other programming languages, the type used to express a truth value is called a `boolean`. A boolean can either have a value of `true` or `false`:

```ts
let my_bool_1: boolean = true;
let my_bool_2: boolean = false;
```

## If Statements

The most fundamental control flow statement is the `if` statement, that tells our program to do one thing **only** if a specific boolean value evaluates to `true`:


[Example #4.0: If Statement](https://playcode.io/1456909)
```ts
let my_bool: boolean = true;
if (my_bool)
{
    console.log('My bool was true!')
}
```

This statement is a little more complicated than the `console.log` statement we learnt about earlier, and comes with both a set of parantheses `()` and curly brackets `{}`.

Inside the parantheses we insert a boolean value, and inside the curly brackets we can insert as much code as we want that should execute if the boolean is true.

### Else statements

When we create an `if` statement, we can also follow it up with an `else` statement, that the program should take if the first path was **not** taken:

[Example #4.1: Else Statement](https://playcode.io/1456911)

```ts
let my_bool: boolean = true;

if (my_bool)
{
  console.log("My bool was true!");
}
else
{
  console.log("My bool was false!");
}
```

### Else if statements

If statements can also be followed by `else if` statements, that adds an additional boolean check if the previous path was not chosen:

[Example #4.2: Else-if Statement](https://playcode.io/1456911)
```ts
let my_bool: boolean = false;
let my_other_bool: boolean = true;

if (my_bool)
{
  console.log("my_bool was true!");
}
else if(my_other_bool)
{
  console.log("my_bool was false, but my_other_bool was true!");
}
else
{
  console.log("Both my_bool and my_other_bool were false!");
}
```

We can have as many else if statements as we want, but we can only have one `else` statement (try to think about why that is).

[Example 4.3: Multiple Else-if Statements](https://playcode.io/1456914)
```ts
let my_bool: boolean = false;
let my_other_bool: boolean = false;
let my_third_bool: boolean = true;

if (my_bool)
{
  console.log("my_bool was true!");
}
else if(my_other_bool)
{
  console.log("my_bool was false, but my_other_bool was true!");
}
else if(my_third_bool)
{
  console.log("Both my_bool and my_other_bool were false, but my_third_bool was true!");
}
else
{
  console.log("all my bools were false!");
}
```

## Boolean Operators

Just like with arithmetic, there are boolean operators that can transform expressions into boolean values. This is much of what makes programming so powerful.

### The `==` operator

`==` is a powerful operator that checks if two different expressions have the same **value**, and resolves into a boolean true/false value that can be used in if statements.

`==` is especially powerful, as it doesn't require that its operands are themselves booleans, but can be both `strings`, `numbers` or almost any other type. Even when its operands are not booleans, the expression resolves into a boolean value.

[Example 4.4: The == Operator](https://playcode.io/1456929)
```ts
let my_first_string = "a";
let my_second_string = "b";

if (my_first_string == my_second_string)
{
  console.log("The two strings are the same");
}
```

The `==` operator has a few siblings that behave similarly:

- `<` works on numbers and checks if one number is **smaller** than the other.
- `>` works on numbers and checks if one number is **larger** than the other
- `!=` works on any type and checks if one value is **not equal** to another value

_Assignment #4.0: Change the above code to print out a message when the two strings are **not** equal ([assignment](https://playcode.io/1456931)) ([solution](https://playcode.io/1456932))_

## The `&&` and `||` operators

The `&&` operator is used to combine two boolean values into a single value that is only true if **both** the operands are also true. This operator is called the "and" operator.

[Example #4.5: The && Operator]()
```ts
let my_first_bool = true;
let my_second_bool = false;

// This line reads like "my_first_bool AND my_second_bool"
if (my_first_bool && my_second_bool)
{
  console.log("Both my_first_bool and my_second_bool are true!")
}
```

The `&&` operator has a sibling called `||`, which combines into a boolean value that is true if **either** of its operands are true. This operator is called the "or" operator.

_Assignment #4.1: Change the above code to print out a message when either my\_first\_bool or my\_second\_bool are equal ([assignment](https://playcode.io/1456938)) ([solution](https://playcode.io/1456939))_