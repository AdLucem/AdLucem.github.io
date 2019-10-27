---
layout: post
title: Talking About GSOC So Far
date: 2019-07-04
category: blog
published: false
---

My first and probably only directly GSOC-related blogpost. Hey, everyone eventually makes at least one of these, right?

I'm working on this experimental Haskell library, Hasktorch- which is, in essence, Haskell's answer to PyTorch. But it being Haskell, there are a lot of pure functions, state changes are difficult, and everything is typed. Even the _types_ are typed, but I haven't looked at the dependently typed version of tensors... yet.

My project started off as "Build a seq2seq example using Hasktorch", but after I looked properly at the library- and at the developing next-release version- I realized that it would be a bit more complicated. Before GSOC, I was looking at the main Hasktorch repository; I'm now working with `ffi-experimental`, which is going to be the next release.


