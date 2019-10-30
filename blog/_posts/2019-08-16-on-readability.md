---
layout: post
title: On Readability and Functional Programs
date: 2019-08-16
category: blog
published: false
---

I'm (rather overenthusiastically) TAing a tiny class of burgeoning functional programmers. Given that "readable code" in functional languages- say, Scheme or Haskell- is quite different from readable code in, say, Python or C++, I thought I'd bring up some particularly eye-searing examples of what not to do.

And in the interests of both humbleness and not offending people, all of these examples are from my own code.

```haskell
inputAsList = [reshape (input @@ x) [1, (size input 1)] | x <- [0.. ((size input 0) - 1)]]
```

This is one single line in a function in one Haskell file of mine. Don't do this. `let` statements, `where` statements, and function composition are your friends. Or at least, the readers' friends.

We haven't come upon the problem of "How the hell does one write modular code with monads" problem yet- that will merit another post of its own.




