Title: Point free style
Date: Mon 29 Dec 2014 05:01:14 PM EST
Tags: haskell

A defining feature of a functional programming language is the ability to compose functions to create a new function: _composability_. Haskell makes it particularly easy to do this:

    f = g . h

where, `g` and `h` are functions and composing them using the `.` (dot) operator creates a new function `f`. This is equivalent to:

    f x = g (h x)

The function `h` is called first with an argument `x`, and the result of this function application is passed as input argument to the other function `g`. So, the return type of function `h` should match the input type of function `g`. Hence the type of the `.` operator:

    (.) :: (b -> c) -> (a -> b) -> (a -> c)

In haskell, operators are functions too. So the `(.)` function takes two functions as arguments and returns a third function composing the two input functions.

The composability extends to any number of functions:

    f x = p (q (r x))

is same as:

    f = p . q . r

This latter style of function definitions (equations) is called [point-free style](https://www.haskell.org/haskellwiki/Pointfree). This style is preferred among Haskell programmers, as it is more compact, and is considered cleaner syntactically (I am not so sure about it though; I think that sometimes point-free style produces less readable code, but it's probably the newbie in me saying it). The HaskellWiki page also says: _It helps the writer (and reader) think about composing functions (high level), rather than shuffling data (low level)._

I wondered why it is called _point-free_, when actually this style has a lot more "points". This was due to my conflating the word "point" with "decimal point" (or "dot").

However, in [Categories Great and Small](http://bartoszmilewski.com/2014/12/05/categories-great-and-small/), the author Bartosz Milewski explains (I am paraphrasing): The form where arguments are explicitly specified is called _point-wise equality_, since the values of arguments are sometimes called points (as in: the value of f at point x). The other form of function equality where arguments are not specified, is therefore, called _point-free equality_.

HaskellWiki further says that this term originated in topology, which deals with spaces composed of points and functions between spaces.

> So a 'points-free' definition of a function is one which does not explicitly mention the points (values) of the space on which the function acts.

Related useful posts:

* [Point-Free Style: What Is It Good For?](http://buffered.io/posts/point-free-style-what-is-it-good-for/)
* [Why/How do you use tacit (point-free) programming?](http://www.reddit.com/r/haskell/comments/1yzh5a/whyhow_do_you_use_tacit_pointfree_programming/)
