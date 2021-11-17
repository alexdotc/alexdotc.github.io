---
title: "Learning Haskell, Part 1"
date: 2021-11-09T18:25:27-05:00
draft: true
---

![Haskell Logo](/img/haskell-logo.svg)

Due to peer pressure from one of the more senior engineers that I work with,
I've started learning Haskell recently. Ok, I admit -- it's only partially due 
to peer pressure. I'd originally wanted to learn more about language parsers, 
interpreters, and compilers, with the goal of creating my own compiler and toy
language for fun. Fanfare for Haskell's parser combinators and the general
utility of Haskell as a great language for this sort of thing pushed me over 
the edge and convinced me to put in the time to learn a language that's quite 
alien (versus popular imperative languages like C, Java, Python etc.). But over
the past few weeks, I've had a lot of fun learning Haskell for it's own
sake. It's truly fascinating and mind-expanding to dive into a functional 
language (and a *purely* functional language, at that).

Since Haskell is so different from other languages I've worked with, and the
first functional language I've used, I figured it would be pertinent to learn
it the right way: reduce assumptions and come at it as someone that almost 
totally new to programming. Experience obviously expedites the process, but I 
think it's helpful to try and avoid drawing too many parallels and come at the
language from a more first-principles approach. With that in mind, I've been
reading the aptly titled *Haskell Programming from First Principles* by Chris
Allen and Julie Moronuki, and doing [all the exercises](https://github.com/alexdotc/haskellbook).

I'm writing this post to reflect on what I've learned so far, mainly as a
series of notes for my own benefit. Perhaps it might also help clarify
something or another for a future Haskell novice. Who knows. I'm around a 
third of the way through the book, and I expect to cover around a sixth
with each post. So this should be 5-6 posts. I will not cover the minutia
in detail, but rather try and stick to the things which have some subtleties,
which I want to crystallize that extra bit for myself, or which are interesting
or unique to Haskell.

## Lambda Calculus

## Expressions

These terms are often used imprecisely or interchangeably, so let's try to be
a little more careful here. An expression is simply some construct of terms
that can has a value (can be evaluated to a result). Expressions can be:

* Something like `5*2*3` that can still be evaluated (reduced).
* Something that has already been fully reduced like `30` or `"Hello"`. We say 
that these are in **normal form**.
* Something that has been partially  evaluated. More on this below 
(Lazy Evaluation), but we can say that this is in **weak head normal form**.
Expressions that are in WHNF are a superset of expressions that are in normal 
form.

## Functions

A function is simply a specific type of expression that can be applied to 
another expression. In Haskell, functions are very much the same as functions
in math, and more specifically in lambda calculus. A function takes one input
(argument) and will always return the same output for each unique input. 
In Haskell, we use `->`, a binary operator, to declare a function at the type
level (not to define it at the term level). For instance, a (non-polymorphic)
type signature for the factorial function might be:

    factorial :: Integer -> Integer
    factorial n = case n of
                    1 -> 1
                    _ -> n * factorial (n-1)

We have a function, factorial, which takes an Integer and returns an Integer.
By the way, this isn't actually a safe factorial function since we haven't
properly handled non-positive integers, but that's a detail that I won't fuss
over in an illustrative blog post.

We could also have something like:
    
    factorialThenMultiply :: Integer -> Integer -> Integer
    factorialThenMultiply n m = case n of
                                  1 -> m
                                  _ -> n * factorialThenMultiply (n-1)

which takes two Integers (one that we are factorial-ing, `n` and one that we
multiply the result of the factorial by, `m`), and returns an Integer (the
product of the factorial and the 2nd argument). But wait a moment, didn't we 
say that functions only take one argument in Haskell? Enter currying.

### Currying

In Haskell, a type signature like

    factorialThenMultiply :: Integer -> Integer -> Integer

is equivalent to

    factorialThenMultiply :: Integer -> (Integer -> Integer)

That is to say that `->` is right associative. Semantically,
`factorialThenMultiply` actually takes 1 Integer and returns *a function that
takes 1 Integer and returns 1 Integer*. This chaining of functions that return
functions is called currying, and we can contrast it with an alternative 
strategy like using a container such as tuple to hold multiple arguments, 
and passing the tuple as the 1 argument. You could still do this in Haskell,
but you'd have to be explicit about it. `(->)` is specifically binary, and each
occurence means "function".

Currying actually provides several advantages over a tuple that I'll get to
below.

### Infix vs Prefix Functions

We're using prefix syntax when we apply a function by placing it's name before
it's arguments. We're using infix syntax when we place a function name between 
it's arguments. **Binary operator** is really just another term for an infix
function in Haskell.

Most functions are obviously prefix, like `factorialThenMultiply`. The function
is called, for example, as:

    factorialThenMultiply 5 4

We also have infix functions such as `+`:

    5 + 4

Haskell lets us use infix order for any function with backticks:

    5 `factorialThenMultiply` 4

Obviously, this is kind of awkward and ugly syntax in this particular case. It
is more useful if we're defining a function 

Conversely, we can use prefix order for infix functions:

    (+) 5 4

It's also useful to realize that this syntax lets us use operators like `+`
as arguments to higher-order functions (functions that take functions as
arguments). For example, the `foldr` function takes 3 arguments, the first
of which is itself a function (called the "folding function").

    -- total sum of numbers in a list
    sum :: Num a => [a] -> a    -- more on this
    sum xs = foldr (+) 0 xs -- in a future post

### Bring Me the $

### Notes

1. You can think of `->` as "function", the same way that you can think of `+`
as "plus". It's simply an infix way to define a function.

2. While `->` is right associative, function *application* (when we use
our prefix functions on the term level) is left associative. This is an
important thing to keep in mind. Obviously, operators like `+` can or `^`
are not necessarily left associative.

## Types

## Miscellanea

### Helpful GHCi commands

    :l file.hs      -- compile file.hs and load into REPL scope
    :i name         -- info about a name like a type constructor, typeclass etc
    :t expression   -- type of an expression
    :k type         -- kind of a type or typeclass
    :m              -- unload modules
    :sp expression  -- show an expression without forcing evaluation (WHNF)
    :?              -- help

## Sources and Thanks

1. [Haskell Programming from First Principles](haskellbook.com) 
    by C. Allen, J. Moronuki

2. My colleague D.S. for fielding questions and seemingly endless patience.
