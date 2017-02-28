---
layout: post
title: Readme Haskell
categories: haskell
tags: haskell
---

* content
{:toc}


# Conception

## Curried function

Currying is when you break down a function that takes multiple arguments into a series of functions that take part of the arguments.
Here's an example in JavaScript:

``` javascript

    function add (a, b) {
      return a + b;
    }

    add(3, 4); returns 7
```
This is a function that takes two arguments, a and b, and returns their sum. We will now curry this function:

    function add (a) {
      return function (b) {
        return a + b;
      }
    }

This is a function that takes one argument, a, and returns a function that takes another argument, b, and that function returns their sum.

    add(3)(4);

    var add3 = add(3);

    add3(4);

The first statement returns 7, like the add(3, 4) statement. The second statement defines a new function called add3 that will add 3 to its argument. This is what some people may call a closure. The third statement uses the add3 operation to add 3 to 4, again producing 7 as a result.

## Task


  [1]: http://stackoverflow.com/questions/36314/what-is-currying
