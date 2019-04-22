---
layout: post
title: Neural Nets For The Layman
date: 2018-04-12
category: blog
cssfile: post.css
---

This is the first of a series of posts aimed at explaining neural networks to a person without a background in machine learning or computer science. 

Neural networks have generated a lot of hype and myth around them, and quite a bit of backlash to that hype and myth by programmers who work in ML.

In truth, neural networks are pretty damn simple as long as you aren't doing the math yourself. Did you take any intro-level statistics courses in college? High school calculus? Basic graph theory? Then you can understand Artificial Neural Networks. 

No, they're really nothing like the human brain. A neural network is basically a very complicated math equation that can "learn" and change itself. What makes them simple to implement is that the very complicated math equation is composed of a single simpler math equation, repeated several times and in different configurations. We'll start with the simpler math equation first. 

## Perceptron

The founding block of a regular neural network is a perceptron (a "neuron"). A perceptron is a mathematical equation that takes 'n' inputs, runs it through a linear equation, puts the result of that equation through another, nonlinear equation, and gives a single output.

To demonstrate graphically, here's a perceptron that takes in 3 inputs: i1, i2 and i3- and returns one output. The 'box'  inside which the equation resides is just for visual effect; it's a linear equation like any other.

<img src="{{ site.baseurl }}/blog/imgs/perceptron_illustration.jpg">

Here's a brightly colored visual depicting precisely "what" the perceptron equation does with the three inputs:

<img src="{{ site.baseurl }}/blog/imgs/perceptron_equation_3_inputs.jpg">

The cool thing about a perceptron is, the linear equation that you're running the inputs through, has _coefficients_ - coefficients that don't have to stay the same, but can be changed by the program. Basically, the equation is of the form ax + by + cz + ... = 0 - your basic linear equation, with as many variables as there are inputs- and a, b, c... are the coefficients, or _parameters_ as they're called technically- which can be changed.

Why do we want to change the parameters? Well, see, we want the perceptron to produce a _specific_ output- the output that we say is correct. Therefore, we don't want any random linear function in the perceptron- we want the _correct_ function, or at least as close to the correct function as we can get. And so we get the "correct" function- or in other words, the set of parameters which give us the least wrong result- by doing the following steps:

1. For a given set of inputs, letting the function guess the output.
2. Checking to see if the function's output is close to the actual output
3. If the function's output is not close to the actual output, we perform some pretty math and change the parameters of the function a little bit, so that (according to our math) the next output the function gives will be closer to the input.

This is the "learning" step of a perceptron, simplified.

The nonlinear 'activation function' basically just converts the result of the linear equation of every perceptron to a number in a particular range- [-1 or +1] in a lot of cases. This is needed because with a lot of applications you just want the answer to "is this thing most probably in category A (-1) or category B (+1)?", and also for another reason- we'll detail that below.

## From Perceptron To Neural Net

So now that we know what a perceptron is, we can easily understand what a neural net is. 

A neural set is basically multiple 'layers' of perceptrons, 'stacked' on top of each other such that the output of any one perceptron in the lower layer goes to _all_ the perceptrons in the layer above it.

<img src="{{ site.baseurl }}/blog/imgs/mlp_illustration.jpg">

Here's the other reason for the nonlinear 'activation function'- try writing out a simple neural networkwith, say, three layers, with one perceptron per layer.

You can see that the output becomes something like f''(f'(f(x))) - which is still a linear equation. So, basically, our 'neural network' still can't learn anything more complicated than a simple line equation.

Putting a nonlinear function at the 'end' of each perceptron, now makes the equation something like: NonLinear(f''(NonLinear(f'(NonLinear(f(x)))))). Now the neural network can learn more complex functions- approximated by composing a non-linear function and a simple linear function.

## From Neural Nets To Deep Learning

So why all the hype around neural nets?

* Neural nets can learn complex, non-linear relationships between data and classes. That is- a single perceptron performs well as long as our data is separated into neat little classes with a straight line; a neural network performs well even if the lines separating our data are complex and squiggly. Thus, NNs perform well on a lot of complex problems like "predict the next word given the previous 'n' words in the sequence as data."
* Slightly tweaked neural networks can 'remember' previous data- Recurrent Neural Networks and LSTMs. 
* Slightly tweaked (but in a different way) neural networks can 'reduce' complex data sequences to simpler sequences **with the important information preserved**- allowing them to, say, "reduce" an image to the important bits (lines, certain shapes, etc.)
* And, most interestingly in NLP, we can **use the parameters that neural networks learn** to extract information about **what the neural network is learning** - as in, we can use the parameters a neural network learns about, say, words in a next-word-prediction task, to make it tell us something about the words it's using to predict. (Word Embeddings, transfer learning)

Each of these will be covered in future posts. Stay tuned!




