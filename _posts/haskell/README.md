---
layout: post
title: Readme Haskell
categories: haskell
tags: haskell
---

* content
{:toc}


# Books

* [LEARN YOU A HASKELL FOR GREAT GOOD][5]

# Conception

## Curried function

[Currying][1] is when you break down a function that takes multiple arguments into a series of functions that take part of the arguments.
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

    ghci> :t (+)
    (+) :: (Num a) => a -> a -> a

    <OR> Like

    (+) :: (Num a) => a -> (a -> a)
    (+) :: (Num a) => 3 -> (4 -> a)

This is a function that takes one argument, a, and returns a function that takes another argument, b, and that function returns their sum.

    add(3, 4);   ==>   add(3)(4);   ==>   var add3 = add(3);
                                          add3(4);

The first statement returns 7, like the add(3, 4) statement. The second statement defines a new function called add3 that will add 3 to its argument. This is what some people may call a closure. The third statement uses the add3 operation to add 3 to 4, again producing 7 as a result.

## fold

[Explanation][2] from stackoverflow.

Think about foldr's very [definition][3]:

     -- if the list is empty, the result is the initial value z
     foldr f z []     = z                  
     -- if not, apply f to the first element and the result of folding the rest 
     foldr f z (x:xs) = f x (foldr f z xs)

So for example `foldr (-) 54 [10,11]` must equal `(-) 10 (foldr (-) 54 [11])`, i.e. expanding again, equal `(-) 10 ((-) 11 54)`.  So the inner operation is `11 - 54`, that is, -43; and the outer operation is `10 - (-43)`, that is, `10 + 43`, therefore `53` as you observe.  Go through similar steps for your second case, and again you'll see how the result forms!


### Explanation 1

Best explanation indeed. Same as how Erik Meijer describes it, i.e., `foldr` is nothing but a replacement of the base case i.e., [] and the cons operator with an accumulator and function of your choosing.

The easiest way to understand `foldr` is to rewrite the list you're folding over without the sugar.

    [1,2,3,4,5] => 1:(2:(3:(4:(5:[]))))

now what `foldr f x` does is that it replaces each `:` with `f` in infix form and `[]` with `x` and evaluates the result.

For example:

    sum [1,2,3] = foldr (+) 0 [1,2,3]

    [1,2,3] === 1:(2:(3:[]))

   so

    sum [1,2,3] === 1+(2+(3+0)) = 6

Try to apply to the 2nd sample:

    foodChain = [human, shark, fish, algae]
    ===>  
    foodChain = (human : (shark : (fish : (algae : []))))

    foldr `eater` 0
    ===>
    human `eater` shark `eater` fish `eater` algae

### Explanation 2

[Come from][4]

foldr is an easy thing:

    foldr :: (a->b->b) -> b -> [a] -> b

It takes a function which is somehow similar to (:),

    (:) :: a -> [a] -> [a]

and a value which is similar to the empty list [],

    [] :: [a]

and replaces each : and [] in some list.

It looks like this:

    foldr f e (1:2:3:[]) = 1 `f` (2 `f` (3 `f` e))

You can imagine foldr as some state-machine-evaluator, too:

f is the transition,

    f :: input -> state -> state

and e is the start state.

    e :: state

foldr (foldRIGHT) runs the state-machine with the transition f and the start state e over the list of inputs, starting at the right end. Imagine f in infix notation as the pacman coming from-RIGHT.

foldl (foldLEFT) does the same from-LEFT, but the transition function, written in infix notation, takes its input argument from right. So the machine consumes the list starting at the left end. Pacman consumes the list from-LEFT with an open mouth to the right, because of the mouth (b->a->b) instead of (a->b->b).

    foldl :: (b->a->b) -> b -> [a] -> b

To make this clear, imagine the function minus as transition:

    foldl (-) 100 [1]         = 99 = ((100)-1)
    foldl (-) 100 [1,2]       = 97 = (( 99)-2) = (((100)-1)-2)
    foldl (-) 100 [1,2,3]     = 94 = (( 97)-3)
    foldl (-) 100 [1,2,3,4]   = 90 = (( 94)-4)
    foldl (-) 100 [1,2,3,4,5] = 85 = (( 90)-5)

    foldr (-) 100 [1]         = -99 = (1-(100))
    foldr (-) 100 [2,1]       = 101 = (2-(-99)) = (2-(1-(100)))
    foldr (-) 100 [3,2,1]     = -98 = (3-(101))
    foldr (-) 100 [4,3,2,1]   = 102 = (4-(-98))
    foldr (-) 100 [5,4,3,2,1] = -97 = (5-(102))

You probably want to use foldr in situations, where the list can be infinite, and where the evaluation should be lazy:

    foldr (either (\l (ls,rs)->(l:ls,rs))
                  (\r (ls,rs)->(ls,r:rs))
          ) ([],[]) :: [Either l r]->([l],[r])


And you probably want to use the strict version of foldl, which is foldl', when you consume the whole list to produce its output. It might perform better and might prevent you from having stack-overflow or out-of-memory exceptions (depending on compiler) due to extreme long lists in combination with lazy evaluation:

    foldl' (+) 0 [1..100000000] = 5000000050000000
    foldl  (+) 0 [1..100000000] = error "stack overflow or out of memory" -- dont try in ghci
    foldr  (+) 0 [1..100000000] = error "stack overflow or out of memory" -- dont try in ghci

The first one –step by step– creates one entry of the list, evaluates it, and consumes it.    

The second one creates a very long formula first, wasting memory with ((...((0+1)+2)+3)+...), and evaluates all of it afterwards.

The third one like the second, but with the other formula.

### Explanation 3

It helps to understand the distinction between `foldr` and `foldl`. Why is `foldr` called "fold right"?

Initially I thought it was because it consumed elements from right to left. Yet both `foldr` and `foldl` consume the list from left to right.

 - `foldl` _evaluates_ from left to right (left-associative) 
 - `foldr` _evaluates_ from right to left (right-associative)

We can make this distinction clear with an example that uses an operator for which associativity matters. We could use a human example, such as the operator, "eats":

    foodChain = (human : (shark : (fish : (algae : []))))

    foldl step [] foodChain
      where step eater food = eater `eats` food  -- note that "eater" is the accumulator and "food" is the element

    foldl `eats` [] (human : (shark : (fish : (algae : []))))
      == foldl eats (human `eats` shark)                              (fish : (algae : []))
      == foldl eats ((human `eats` shark) `eats` fish)                (algae : [])
      == foldl eats (((human `eats` shark) `eats` fish) `eats` algae) []
      ==            (((human `eats` shark) `eats` fish) `eats` algae)

The semantics of this `foldl` is:
  - A human eats some shark,
  - and then the same human who has eaten shark then eats some fish, etc.
  - `The eater is the accumulator`.

Contrast this with:

    foldr step [] foodChain
        where step food eater = eater `eats` food.   -- note that "eater" is the element and "food" is the accumulator

    foldr `eats` [] (human : (shark : (fish : (algae : []))))
      == foldr eats (human `eats` shark)                              (fish : (algae : []))))
      == foldr eats (human `eats` (shark `eats` (fish))               (algae : [])
      == foldr eats (human `eats` (shark `eats` (fish `eats` algae))) []
      ==            (human `eats` (shark `eats` (fish `eats` algae) 

The semantics of this `foldr` is:
  - A human eats a shark
    - and the shark which has already eaten a fish,
    - and the fish which has already eaten some algae.
  - `The food is the accumulator`.

Both `foldl` and `foldr` "peel off" eaters from left to right, so that's not the reason we refer to foldl as "left fold". Instead, the order of evaluation matters.

## Task


  [1]: http://stackoverflow.com/questions/36314/what-is-currying
  [2]: http://stackoverflow.com/questions/1757740/how-foldr-works
  [3]: http://www.haskell.org/haskellwiki/Fold
  [4]: http://stackoverflow.com/questions/3950508/haskell-foldr-and-foldl-further-explanation-and-example
  [5]: https://learnyoua.haskell.sg/content/zh-cn/index.html
