---
layout: post
title: Aligning (Hindi) Sentence Chunks With Dependency Parsing
date: 2018-04-15
category: blog
cssfile: post.css
published: false
---

So this is a bit of an odd problem, in that it doesn't usually show up in the standard leaderboard of NLP tasks.

You have a sentence. You have a different sentence, with more or less the same meaning, and with similar syntactic structure, but most of your words/word groups are different. Say, "That man was sesquipedally loquacious", and "That there man, 'e was an eloquent bloviator".

Example provided to give some context of the problem. The actual reason why I'm working on this is that I have a machine-translation system. I have the source language sentences, I have their corresponding machine-generated target language sentences, and I have their corresponding _postedited_ sentences- where, for each machine-translated sentence, a hired human looked at it, decided which bits needed improvement, and then rewrote it accordingly.

What I want to do here is    


