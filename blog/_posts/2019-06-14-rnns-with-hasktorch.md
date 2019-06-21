---
layout: post
title: RNNs with Hasktorch
date: 2019-06-14
category: blog
cssfile: post.css
published: true
---

Credit to Adam Paszke, creator of PyTorch, for this portion of the code- it's pretty much lifted and slightly modified from [his MLP example for Hasktorch.](https://github.com/hasktorch/ffi-experimental/blob/master/examples/xor_mlp/Main.hs)

Adam Paszke's Linear layer type:

```haskell
data LinearSpec = LinearSpec { in_features :: Int, out_features :: Int }
  deriving (Show, Eq)

data Linear = Linear { weight :: Parameter, bias :: Parameter }
  deriving (Show)
```

The Recurrent layer type that's slightly modified from the above:

```haskell
data RecurrentSpec = RecurrentSpec { in_features :: Int, hidden_features :: Int, nonlinearitySpec :: Tensor -> Tensor }


data Recurrent = Recurrent { weight_ih :: Parameter, bias_ih :: Parameter,
                             weight_hh :: Parameter, bias_hh :: Parameter
                        }
```

Input gate:

```haskell
inputGate weight bias input = (matmul (toDependent weight) (matrixTranspose input)) + (matrixTranspose $ toDependent bias)
```

Hidden layer gate:

```haskell
hiddenGate weight bias hidden = (matmul (toDependent weight) (matrixTranspose hidden)) + (matrixTranspose $ toDependent bias)
```

And finally, the updated hidden state:

```haskell
h' ig hg = matrixTranspose $ Torch.Functions.tanh (ig + hg)
```

Wrapping it together in one function:

```haskell
recurrent :: Recurrent -> Tensor -> Tensor -> Tensor
recurrent Recurrent{..} input hidden =
    h' (ig weight_ih bias_ih input) (hg weight_hh bias_hh hidden)
```

---------------------------------------------------

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

This is simple to calculate for the weights in the output layer:


Generalizing this over 'n' layers, dE/dW for any given internal layer 'n' will be:

<<general equation for any internal layer>>

So finally we reach the 'original' RNN at the first timestep- or in the unrolled version, the first layer- and update the weights.

<<updated RNN code>>

Despite the entire backpropagation, though, the only updated RNN we want is the updated version at timestep zero. So we slice off the 'first' layer:

<<slicing off updated RNN code>>

And we have our updated RNN.







