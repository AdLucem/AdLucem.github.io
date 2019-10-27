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

