---
layout: post
title: RNNs with Hasktorch
date: 2019-06-14
category: blog
cssfile: post.css
published: false
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

<<1.0 function composition view of elman cell>>

I like the function composition view of neural networks better than the block view. I also like my math equations really, *really* colourful.

What we're trying to optimize is our error function:

<<error function in terms of RNN>>

Remember the gradient descent update equation? Well, we need to update our unrolled-rnn weights in each layer according to that.

<<gradient descent update equation>>

So we need the gradient of our error function in terms of the RNN weights:

<<dE/dW>>

But the error function is in terms of the RNN itself. We could substitute the RNN function showed in 1.0, like we do in linear regression:

<<linear regression error function equation>> 

But with something this large, it gets ugly. So we use the chain rule of differentiation to make it simpler. So for each weight, the derivative of the error function with respect to that particular weight works out as:

<<error function derivative, chain rule>>

Generalizing this over 'n' layers, dE/dW for any given internal layer 'n' will be:

<<general equation for any internal layer>>

So finally we reach the 'original' RNN at the first timestep- or in the unrolled version, the first layer- and update the weights.

<<updated RNN code>>

Despite the entire backpropagation, though, the only updated RNN we want is the updated version at timestep zero. So we slice off the 'first' layer:

<<slicing off updated RNN code>>

And we have our updated RNN.







