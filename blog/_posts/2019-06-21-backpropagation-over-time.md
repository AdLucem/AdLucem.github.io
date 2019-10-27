---
layout: post
title: Backpropagation Over Time
date: 2019-06-21
category: blog
cssfile: post.css
published: true
---

So, how to work out backprop over multiple timesteps?

Most posts explain backprop through time as "unrolling" the RNN into a multilayer NN- with each timestep representing one layer- and each layer having the same parameters. This is simple enough in an Elman cell, where the only output of each timestep is the updated hidden state. Here, though, I'll be working out the math of it- using as an example, an elman cell that's run for three timesteps for a 3-unit sequence:
 
<img src="{{ site.baseurl }}/blog/imgs/rnn_as_composed_functions.png">

I like the function composition view of neural networks better than the block view. I also like colorful math equations- it helps me parse the components more easily.

What we're trying to optimize is our loss/error function. For simplicity, we'll use MSE as our loss function here:

<img src="{{ site.baseurl }}/blog/imgs/err_function_in_terms_of_rnn.png">

Remember the gradient descent update equation? Well, we need to update our unrolled-rnn weights in each layer according to that.

<img src="{{ site.baseurl }}/blog/imgs/sgd_update_rule.png">

So we need the gradient of our error function in terms of the RNN weights:

<img src="{{ site.baseurl }}/blog/imgs/error_derivative_wrt_weight.png">


But the error function is in terms of the RNN network function, not the individual weights. We could substitute the RNN function itself in place of the `RNN` term, like we do in linear regression.

But with something this large, it gets ugly. So we use the chain rule of differentiation to make it simpler. So for each weight, the derivative of the error function with respect to that particular weight works out as:

<img src="{{ site.baseurl }}/blog/imgs/error_equation_chain_rule.png">

This is simple to calculate for the weights in the last timestep, i.e: the "output layer":

<img src="{{ site.baseurl }}/blog/imgs/final_timestep_rnn_grad.png">

Backpropagating to the middle timestep:

<img src="{{ site.baseurl }}/blog/imgs/second_timestep_rnn_grad.png">

So finally we reach the 'original' RNN at the first timestep- or in the unrolled version, the first layer:

<img src="{{ site.baseurl }}/blog/imgs/first_timestep_rnn_grad.png">

As for what each partial derivative looks like, for an elman cell:

<img src="{{ site.baseurl }}/blog/imgs/elman_cell_function.png">

Taking one of the parameters as example:

<img src="{{ site.baseurl }}/blog/imgs/elman_cell_derivative_parameters.png">
