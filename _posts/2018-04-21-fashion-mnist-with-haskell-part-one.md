---
layout: post
title: Fashion-MNIST With Haskell, Part 1
date: 2018-04-21
---

This is a short writeup on how to use `mnist-idx` and `hasktorch` to load files from the IDX data format- used in the original MNIST as well as [fashion-MNIST](https://github.com/zalandoresearch/fashion-mnist) - into memory in Haskell, as tensors.

## Getting Hasktorch Running

## Using Hasktorch In A Cabal Project

The most recent work done on Hasktorch is not on the `cabal` package list as of now. If you're reading this after Hasktorch's released a stable build, then by all means use `cabal` or `stack` to install it. Else, here's a little workaround on how to connect hasktorch to your cabal project file:

 

