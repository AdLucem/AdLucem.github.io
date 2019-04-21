---
layout: post
title: Neural Nets
date: 2018-04-12
---

This is the first of a series of posts 

Neural networks have generated a lot of hype and myth around them, and quite a bit of backlash to that hype and myth by programmers who work in ML.

In truth, neural networks are pretty damn simple as long as you aren't doing the math yourself. Did you take any intro-level statistics courses in college? High school calculus? Basic graph theory? Then you can understand NNs.

## Perceptron

Let's start with the founding block of a NN- a perceptron (a "neuron"). A perceptron is a mathematical equation that takes 'n' inputs, runs it through a linear equation, puts the result of that equation through another, nonlinear equation, and gives a single output.

To demonstrate graphically, here's a perceptron that takes in 3 inputs: i1, i2 and i3

<img src="{{ site.baseurl }}/imgs/perceptron_illustration.jpg" align="left" height="500" width="2000">

Here's a brightly colored visual depicting precisely "what" the perceptron equation does with the three inputs:

<img src="{{ site.baseurl }}/imgs/perceptron_equation_3_inputs.jpg" align="left" height="500" width="1000">

The cool thing about a perceptron is, the linear equation that you're running the inputs through, has _coefficients_ - coefficients that don't have to stay the same, but can be changed by the program. Basically, the equation is of the form ax + by + cz + ... = 0 - your basic linear equation, with as many variables as there are inputs- and a, b, c... are the coefficients, or _parameters_ as they're called technically- which can be changed.

Why do we want to change the parameters? Well, see, we want the perceptron to produce a _specific_ output- the output that we say is correct. Therefore, we don't want any random linear function in the perceptron- we want the _correct_ function, or at least as close to the correct function as we can get. And so we get the "correct" function- or in other words, the set of parameters which give us the least wrong result- by doing the following steps:

(1) For a given set of inputs, letting the function guess the output.
(2) Checking to see if the function's output is close to the actual output
(3) If the function's output is not close to the actual output, we perform some pretty math and change the parameters of the function a little bit, so that (according to our math) the next output the function gives will be closer to the input.

The nonlinear 'activation function' basically just converts the result of the linear equation to a number in a set- [-1 or +1] in a lot of cases. This is important because with a lot of applications you just want the answer to "is this thing most probably in category A (-1) or category B (+1)?"

## From Perceptron To Neural Net

So now that we know what a perceptron is, we can easily understand what a neural net is. 

A neural set is basically multiple 'layers' of perceptrons, 'stacked' on top of each other such that the output of any one perceptron in the lower layer goes to _all_ the perceptrons in the layer above it.

<img src="{{ site.baseurl }}/imgs/mlp_illustration.jpg" align="left" height="500" width="1000">

(In practice, this may not always happen- there are neural net alterations like attention and dropout, that sever some of the connections between layers or skip some layers or add some layers. These are mostly domain-specific neural net alterations, and I'll get to them in a bit.)

So why all the hype around neural nets?

- Neural nets can learn complex, non-linear relationships between data and classes. That is- a single perceptron performs well as long as our data is separated into neat little classes with a straight line; a neural network performs well even if the lines separating our data are complex and squiggly. Thus, NNs perform well on a lot of complex problems like "predict the next word given the previous 'n' words in the sequence as data."
- Slightly tweaked neural networks can 'remember' previous data- Recurrent Neural Networks and LSTMs. 
- Slightly tweaked (but in a different way) neural networks can 'reduce' complex data sequences to simpler sequences **with the important information preserved**- allowing them to, say, "reduce" an image to the important bits (lines, certain shapes, etc.)
- And, most interestingly in NLP, we can **use the parameters that neural networks learn** to extract information about **what the neural network is learning** - **and other things too** (Word Embeddings, transfer learning)




