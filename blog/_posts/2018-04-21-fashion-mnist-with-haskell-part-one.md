---
layout: post
title: Fashion-MNIST With Hasktorch
date: 2019-04-21
category: blog
cssfile: post.css
published: false
---

This is a short writeup on using Hasktorch- a Haskell tensors and machine learning library- to  train a model to classify images from the <a href="https://github.com/zalandoresearch/fashion-mnist">fashion-MNIST</a> dataset.

Since Haskell is a library in early development at the time of writing this, using it can be a bit tricky. See <a href="https://adlucem.github.io/blog/2019/05/10/hasktorch-using-a-haskell-library-not-in-the-package-manager.html">this post</a> for how to set things up.

## Loading It Into Memory

I've written a <a href="https://github.com/AdLucem/gru-encoder-decoder/blob/master/fashion-mnist/src/DataLoader.hs"> dataloader for the MNIST format </a> that uses Haskell's MNIST-IDX library to load the data from IDX files to tensors. 

##
