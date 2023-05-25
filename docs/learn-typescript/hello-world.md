---
layout: default
title: Hello World
nav_order: 1
---

# Lesson 1: Hello World

In this lesson, we will learn how to set up a temporary coding environment for the following lessons and write our first program.

You don't need to install anything to follow these tutorials. Instead, we will use an online code editor called _PlayCode_ for code examples and assignments.

## The basic code window

PlayCode is a simple online code editor that allows us to easily write TypeScript code and immediately see its effects

[[Insert screenshot of playcode with things marked out]]

* **Code Editor** - This is where we can type in TypeScript code.
* **Output Window** - This is a window that allows us to see text that our TypeScript program can write.

In PlayCode, what we write in the _Code Editor_ will automatically run and produce text in the _Output Window_ as we type it, starting over anytime we change anything.

Normally, this doesn't happen automatically this way but it is just how this online editor works. In TSWoW, we would use commands such as `build data` to manually run our program.

## Hello World

Writing out text into the _Output Window_ is commonly called _logging_ or _printing_. 

[Example: Hello World](https://playcode.io/1456694)

In our code window, we have the code `console.log("Hello World!");`, and in the output window we can see our program produces the text `"Hello World!"`. This is known as a print statement, and we can divide up into two parts:

- **Log Statement** - `console.log()` - Statement used to write to the output window. This is known as a "function call", which we will become more familiar with in future tutorials.

- **Text** - `"Hello World!"` - Piece of text we want written, surrounded by double quotes. This is known as a "string", which is a programming term referring to 'text' data.

To use the `console.log()` statement, we simply put a 'string' inside of its parentheses, but remember to include the double quotes!

_**Assignment #1.1**: Make the program instead print out the text "Hello TSWoW!"_
([Assignment](https://playcode.io/1456807)) ([Solution](https://playcode.io/1456803))

_**Assignment #1.2**: Change the program to write out "Hello TSWoW!" twice._
([Assignment](https://playcode.io/1456803)) ([Solution](https://playcode.io/1456804))