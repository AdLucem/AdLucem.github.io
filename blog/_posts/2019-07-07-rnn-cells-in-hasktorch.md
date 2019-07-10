---
layout: post
title: Writing RNN Cells For Hasktorch
date: 2019-07-07
category: blog
published: true
---

So, what's the big issue with writing an RNN cell? It's just cell weights, and a function `Cell Weights -> Input -> Previous Hidden Layer -> Output`, right?

Sure, we can write an RNN type that way. Say, this simple Elman cell type:

```haskell
data ElmanCell = ElmanCell { weight_ih :: Parameter, bias_ih :: Parameter,
                             weight_hh :: Parameter, bias_hh :: Parameter,
                           }
```

Or this GRU cell type:

```haskell
data GRUCell = GRUCell {weight_ir :: Parameter, bias_ir :: Parameter,
                        weight_iz :: Parameter, bias_iz :: Parameter,
                        weight_in :: Parameter, bias_in :: Parameter,
                        weight_hr :: Parameter, bias_hr :: Parameter,
                        weight_hz :: Parameter, bias_hz :: Parameter,
                        weight_hn :: Parameter, bias_hn :: Parameter
                      }
```

... Uh oh. 

The thing is, a recurrent cell is basically a weighted combination of "gates"- each gate being a linear function of the form:

<img src="{{ site.baseurl }}/blog/imgs/linear_function_rnn.png">

But with something like an LSTM, which has three gates- or even a GRU, which has only two- plus the weights that work on combining the results of the gates- writing out a recurrent cell in pure function form (i.e: only weights, inputs and the combination thereof), gets unweildy and repetitive. And well, this is Haskell, where everything is supposed to be a two-line function composed of smaller two-line functions. 

Given that we already have a Linear layer type in Hasktorch, we add another type- a *NonLinear* layer, or a layer with weights, bias and activation function. This is our type representing a Gate.

```haskell
data RecurrentGate = RecurrentGate {
    weight_input :: Parameter,
    weight_hidden :: Parameter,
    bias :: Parameter,
    nonLinearity :: Tensor -> Tensor
}

gate :: RecurrentGate -> Tensor -> Tensor -> Tensor
gate RecurrentGate{..} input hidden = 
  nonLinearity $ (mul input inputWt) + (mul hidden hiddenWt) + bias 
  where
    mul features wts = matmul (toDependent weight) (transpose2D features)
```

The Elman cell can now be represented as a single gate. Or rather, as a type synonym for a recurrent gate:

```haskell
type ElmanCell = RecurrentGate
```

The gate structure, however, becomes useful when we're writing a more complex recurrent cell type like an LSTM:

```haskell
data LSTMCell = LSTMCell {
  input_gate   :: RecurrentGate,
  forget_gate  :: RecurrentGate,
  output_gate  :: RecurrentGate,
  hidden_gate  :: RecurrentGate,
  cell_state   :: Parameter 
}
```

Not to mention, it makes writing the function for the cell a lot more concise:

```haskell
-- To calculate the new cell state (memory)
newCellState :: LSTMCell -> Tensor -> Tensor -> Tensor
newCellState LSTMCell{..} input hidden =
  (fg * cell_state) + (ig * c')
  where
    ig = gate input_gate input hidden
    fg = gate forget_gate input hidden
    c' = gate hidden_gate input hidden

-- to calculate the new hidden state
instance RecurrentCell LSTMCell where
  nextState cell input hidden =
    og * (Torch.Functions.tanh cNew)  
    where
      og = gate (output_gate cell) input hidden
      cNew = newCellState cell input hidden
```

Thus, we can represent the function for an LSTM as a combination of values returned by gates.

The GRU cell is also written accordingly- and it's easier because a GRU cell is simpler than an LSTM, having only two gates and no cell memory.