---
layout: default
title: Arithmetic
nav_order: 3
---

# Lesson 3: Arithmetic

An _operator_ is a special type of code typically used to modify a piece of data in some way. Some common operators include mathematical operators `+`, `-`, `*`, `/`.

[Example 3.0](https://playcode.io/1456927)
```ts
let my_number: number = 20;
let my_other_number: number = my_number + 5;
console.log(my_other_number)
```

As we can see, writing math formulas in code looks very similar to how it would on a piece of paper, with the important difference that the `=` operator is not used to merely _state_ that two expressions are the same, but to **assign** the right-hand expression into a variable on the left.

It is not possible to use the `=` operator for anything but a variable on the left-hand side.

_**Assignment #3.1**: Make the above program instead print out the value "100" by changing the operator._ ([Assignment](https://playcode.io/1483810)) ([Solution](https://playcode.io/1483821))

## String Operations

The `+` symbol can also be used on string objects, where it instead acts as a "join" operator to concatenate two string values together into a single string.

[Example 3.1](https://playcode.io/1456927)
```ts
let my_first_string: string = "Hello ";
let my_second_string: string = "World!";
let my_third_string: string = my_first_string + my_second_string;
console.log(my_third_string)
```

_**Assignment #3.2**: Try using the `+` operator between a string and a number. What happens and why?_