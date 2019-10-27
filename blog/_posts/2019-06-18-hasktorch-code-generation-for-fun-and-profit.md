---
layout: post
title: Hasktorch Code Generation For Fun And Profit
date: 2019-06-18
category: blog
cssfile: post.css
published: false
---

Because I got tired of thinking "What the _hell_ is going on here?!" every night, this is a python script to parse the bindings in `hasktorch/ffi-experimental`- and to both generate some sparse documentation for each and generate a possibly-working Hasktorch function for each using a template. 

It's something I do fairly often for large lists of information that I need to parse. I'm also writing it in Python, because this is quick-and-dirty text wrangling and Python is my quick-and-dirty language of choice for that.

Firstly, setting up a class for the FFI bindings. Each binding is a function type signature and below it, a function. So:

```python

```

Yes, this is the most primitive of text parsing. I am writing this in a sudden burst of irritability and will not take any criticism. 
