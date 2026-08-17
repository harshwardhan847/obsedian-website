# Building a Tiny LLM from Scratch in JavaScript

  

## Learning Notes — Neural Networks Fundamentals

  

> Goal: Build a tiny language model from scratch in JavaScript to understand the fundamentals behind neural networks and eventually Transformers/LLMs.

  

---

  

# 1. The Big Picture

  

We are learning machine learning by implementing the important pieces ourselves instead of starting with a framework such as TensorFlow, PyTorch, or Hugging Face.

  

The progression is:

  

```text

Neuron

↓

Activation functions

↓

Loss

↓

Gradient

↓

Gradient descent

↓

Backpropagation

↓

Multiple neurons

↓

Hidden layers

↓

Neural network

↓

Tokenization

↓

Language model

↓

Embeddings

↓

Attention

↓

Transformer

↓

Tiny GPT-like model

```

  

The central training idea is:

  

```text

Input

↓

Forward pass

↓

Prediction

↓

Loss

↓

Backpropagation

↓

Gradients

↓

Update weights

↓

Repeat

```

  

---

  

# 2. What Is a Neuron?

  

A neural-network neuron is essentially a mathematical function that takes inputs, multiplies them by weights, adds a bias, and applies an activation function.

  

The basic equation is:

  

```text

z = x₁w₁ + x₂w₂ + ... + xₙwₙ + b

  

output = activation(z)

```

  

Where:

  

- `x` = input

- `w` = weight

- `b` = bias

- `z` = weighted sum

- `activation` = activation function

- `output` = neuron's prediction

  

For two inputs:

  

```text

z = x₁w₁ + x₂w₂ + b

```

  

Then:

  

```text

output = activation(z)

```

  

---

  

# 3. Intuition Behind Weights

  

Weights determine how strongly each input influences the neuron.

  

Suppose:

  

```text

input 1 = 2

input 2 = 3

  

weight 1 = 0.5

weight 2 = -0.2

bias = 0.1

```

  

Then:

  

```text

z = (2 × 0.5) + (3 × -0.2) + 0.1

  

= 1 - 0.6 + 0.1

  

= 0.5

```

  

The weights are the parameters the neural network learns.

  

A neural network starts with arbitrary/random weights and changes them during training.

  

---

  

# 4. Bias

  

The bias is an additional parameter added to the weighted sum:

  

```text

z = x₁w₁ + x₂w₂ + b

```

  

It allows the neuron to shift its output independently of the inputs.

  

For example, if all inputs are zero:

  

```text

x₁ = 0

x₂ = 0

```

  

then:

  

```text

z = b

```

  

So the bias still affects the output.

  

---

  

# 5. Activation Functions

  

Without an activation function, a neuron is essentially just doing linear arithmetic.

  

We use an activation function to transform the weighted sum.

  

For our first network we used:

  

```js

Math.tanh(x)

```

  

`tanh` stands for hyperbolic tangent.

  

It squashes numbers into the range:

  

```text

-1 < tanh(x) < 1

```

  

Examples:

  

```text

tanh(-3) ≈ -0.995

tanh(-2) ≈ -0.964

tanh(-1) ≈ -0.762

tanh(0) = 0

tanh(1) ≈ 0.762

tanh(2) ≈ 0.964

tanh(3) ≈ 0.995

```

  

So we can think of it as a squashing function:

  

```text

large negative number → close to -1

0 → 0

large positive number → close to +1

```

  

---

  

# 6. Why Do We Need an Activation Function?

  

Suppose we only had:

  

```text

output = x₁w₁ + x₂w₂ + b

```

  

Stacking many such operations would still produce a linear transformation.

  

Neural networks need nonlinear functions to learn nonlinear relationships.

  

With an activation:

  

```text

inputs

↓

weighted sum

↓

activation

↓

output

```

  

the network can model more complicated relationships.

  

This becomes especially important when we add hidden layers.

  

---

  

# 7. Tanh Saturation

  

`tanh` has an important property called saturation.

  

For sufficiently large values:

  

```text

tanh(10) ≈ 1

tanh(100) ≈ 1

tanh(1000) ≈ 1

```

  

For sufficiently negative values:

  

```text

tanh(-10) ≈ -1

tanh(-100) ≈ -1

```

  

This means that very large inputs to `tanh` produce outputs very close to `-1` or `1`.

  

This becomes important during training because the derivative of `tanh` becomes very small in these saturated regions.

  

---

  

# 8. First Neuron in JavaScript

  

Our initial implementation was:

  

```js

function tanh(x) {

return Math.tanh(x);

}

  

function neuron(inputs, weights, bias) {

let sum = bias;

  

for (let i = 0; i < inputs.length; i++) {

sum += inputs[i] * weights[i];

}

  

return tanh(sum);

}

```

  

Example:

  

```js

const inputs = [2, 3];

const weights = [0.5, -0.2];

const bias = 0.1;

  

const output = neuron(inputs, weights, bias);

  

console.log(output);

```

  

The important point:

  

> At this stage the neuron is NOT learning.

  

We manually supplied the weights and bias.

  

---

  

# 9. Machine Learning Means Learning the Parameters

  

Instead of manually deciding:

  

```js

weights = [0.5, -0.2]

bias = 0.1

```

  

we start with random values:

  

```js

let weights = [

Math.random() * 2 - 1,

Math.random() * 2 - 1

];

  

let bias = Math.random() * 2 - 1;

```

  

The model then tries to find useful values for these parameters.

  

The training process is:

  

```text

Random parameters

↓

Prediction

↓

Measure error

↓

Determine how parameters caused the error

↓

Update parameters

↓

Repeat

```

  

---

  

# 10. Forward Pass

  

The forward pass is the process of taking the input through the network to produce a prediction.

  

For a single neuron:

  

```text

input

↓

multiply by weights

↓

add results

↓

add bias

↓

tanh

↓

prediction

```

  

Mathematically:

  

```text

z = x₁w₁ + x₂w₂ + b

  

prediction = tanh(z)

```

  

In code:

  

```js

function forward(inputs, weights, bias) {

let sum = bias;

  

for (let i = 0; i < inputs.length; i++) {

sum += inputs[i] * weights[i];

}

  

return Math.tanh(sum);

}

```

  

---

  

# 11. Training Data

  

We first trained our neuron on a simple OR-like problem:

  

```js

const data = [

{ inputs: [1, 1], target: 1 },

{ inputs: [1, 0], target: 1 },

{ inputs: [0, 1], target: 1 },

{ inputs: [0, 0], target: -1 }

];

```

  

The model sees examples rather than being given an explicit `if` statement.

  

The intended relationship is:

  

```text

[1,1] → 1

[1,0] → 1

[0,1] → 1

[0,0] → -1

```

  

The model tries to discover parameters that produce these outputs.

  

---

  

# 12. Prediction vs Target

  

For every training example we have:

  

```text

prediction = what the model produced

target = correct answer

```

  

Example:

  

```text

prediction = 0.8

target = 1

```

  

The model is close, but not perfect.

  

Another example:

  

```text

prediction = -0.5

target = 1

```

  

The model is much more wrong.

  

We need a numerical way to represent how wrong the prediction is.

  

That is the purpose of a loss function.

  

---

  

# 13. Loss Function

  

Our first loss function was:

  

```js

function loss(prediction, target) {

return (prediction - target) ** 2;

}

```

  

Mathematically:

  

```text

Loss = (prediction - target)²

```

  

---

  

# 14. Why Do We Square the Error?

  

Suppose:

  

```text

target = 10

```

  

If:

  

```text

prediction = 7

```

  

then:

  

```text

prediction - target = -3

```

  

If:

  

```text

prediction = 13

```

  

then:

  

```text

prediction - target = +3

```

  

Both predictions are equally far from the target.

  

If we didn't square the error, we could get:

  

```text

-3

+3

```

  

and these could cancel when errors are combined.

  

Squaring gives:

  

```text

(-3)² = 9

(+3)² = 9

```

  

Now both errors are positive.

  

Another useful property is that large errors are punished more strongly:

  

```text

error = 1 → squared error = 1

error = 2 → squared error = 4

error = 3 → squared error = 9

error = 10 → squared error = 100

```

  

This particular loss for one example is squared error.

  

When we average squared errors over examples, it becomes Mean Squared Error (MSE).

  

---

  

# 15. Total Loss

  

Our dataset contains multiple examples.

  

For example:

  

```text

Input Target Prediction Loss

  

[1,1] 1 0.8 0.04

[1,0] 1 0.6 0.16

[0,1] 1 0.7 0.09

[0,0] -1 -0.5 0.25

```

  

Total loss:

  

```text

0.04 + 0.16 + 0.09 + 0.25

= 0.54

```

  

In code:

  

```js

let totalLoss = 0;

  

for (const sample of data) {

const prediction = forward(

sample.inputs,

weights,

bias

);

  

const error = loss(

prediction,

sample.target

);

  

totalLoss += error;

}

```

  

`totalLoss` is a measure of how badly the model is performing across the entire dataset.

  

It is useful for monitoring training.

  

For example:

  

```text

Epoch 0 Loss: 3.82

Epoch 1000 Loss: 1.72

Epoch 2000 Loss: 0.83

Epoch 3000 Loss: 0.31

Epoch 4000 Loss: 0.08

```

  

A decreasing loss generally indicates that the model is improving.

  

For conventional MSE, we would divide by the number of samples:

  

```js

const meanLoss = totalLoss / data.length;

```

  

---

  

# 16. Epoch

  

An epoch means one complete pass through the training dataset.

  

If we have:

  

```text

4 training examples

```

  

then processing all four once is one epoch.

  

Our training code used:

  

```js

for (let epoch = 0; epoch < 10000; epoch++) {

// training

}

```

  

So the dataset was processed 10,000 times.

  

---

  

# 17. The Main Problem: How Do We Change the Weights?

  

Suppose:

  

```text

weight = 0.5

loss = 0.8

```

  

We know the model is wrong.

  

But how do we know whether to change:

  

```text

0.5 → 0.6

```

  

or:

  

```text

0.5 → 0.4

```

  

?

  

We need to know how the loss changes when the parameter changes.

  

This is what a gradient tells us.

  

---

  

# 18. What Is a Gradient?

  

For a single parameter, a gradient can be understood as the slope of the loss with respect to that parameter.

  

Imagine:

  

```text

Loss

↑

|

10 | *

| /

5 | *

| /

0 |__*____________→ Weight

```

  

The gradient tells us the direction in which the loss is increasing.

  

We want to move in the opposite direction to reduce the loss.

  

This gives us gradient descent.

  

---

  

# 19. Gradient Descent

  

The basic update rule is:

  

```text

parameter = parameter - learningRate × gradient

```

  

For a weight:

  

```js

weight -= learningRate * gradient;

```

  

The gradient tells us the direction.

  

The learning rate tells us how large a step to take.

  

---

  

# 20. Learning Rate

  

The learning rate controls how much the parameters change during each update.

  

Example:

  

```text

learningRate = 0.001

```

  

means tiny updates.

  

```text

learningRate = 0.1

```

  

means larger updates.

  

```text

learningRate = 1

```

  

means even larger updates.

  

In our experiment:

  

```text

0.001 → learned, but more slowly

0.1 → learned very well

1 → learned very well

```

  

This does NOT mean `1` is universally better.

  

The best learning rate depends on:

  

- model architecture

- data

- initialization

- loss function

- optimization method

- scale of gradients

  

A learning rate that is too small can make training extremely slow.

  

A learning rate that is too large can cause unstable updates, overshooting, exploding parameters, or failure to converge.

  

---

  

# 21. What Happened With Learning Rate 10+?

  

We tested:

  

```js

const learningRate = 10;

```

  

and got:

  

```text

[1, 1] → 1

[1, 0] → 1

[0, 1] → 1

[0, 0] → 1

```

  

Even though `[0,0]` should have been `-1`.

  

This was NOT a perfect result.

  

It happened because `tanh` saturates.

  

If the bias becomes very large and positive:

  

```text

bias = 20

```

  

then for `[0,0]`:

  

```text

z = 0 × w₁ + 0 × w₂ + bias

= 20

```

  

and:

  

```text

tanh(20) ≈ 1

```

  

So the neuron predicts `1`.

  

Large learning rates can push parameters into bad regions.

  

---

  

# 22. Derivative of Tanh

  

We used:

  

```js

const tanhGradient =

1 - prediction ** 2;

```

  

Why?

  

If:

  

```text

prediction = tanh(z)

```

  

then:

  

```text

d(tanh(z))/dz = 1 - tanh(z)²

```

  

Because:

  

```text

prediction = tanh(z)

```

  

we can write:

  

```text

tanhGradient = 1 - prediction²

```

  

So in JavaScript:

  

```js

const tanhGradient =

1 - prediction ** 2;

```

  

Example:

  

```text

prediction = 0.8

  

tanhGradient

= 1 - 0.8²

= 1 - 0.64

= 0.36

```

  

If prediction is close to `1`:

  

```text

1 - 1² = 0

```

  

If prediction is close to `-1`:

  

```text

1 - (-1)² = 0

```

  

This explains why saturated `tanh` neurons can have very small gradients.

  

---

  

# 23. The Error Gradient

  

Our loss is:

  

```text

Loss = (prediction - target)²

```

  

We want to know:

  

```text

How does the loss change when prediction changes?

```

  

The derivative is:

  

```text

dLoss/dPrediction

=

2 × (prediction - target)

```

  

So:

  

```js

const errorGradient =

2 * (prediction - target);

```

  

Example:

  

```text

prediction = 0.8

target = 1

  

errorGradient

= 2 × (0.8 - 1)

= -0.4

```

  

This tells us the direction and sensitivity of the loss with respect to the prediction.

  

---

  

# 24. Chain Rule

  

Our neuron is a sequence of operations:

  

```text

input

↓

input × weight

↓

sum + bias

↓

tanh

↓

prediction

↓

loss

```

  

We want to know:

  

```text

How does changing the weight affect the loss?

```

  

There are multiple operations between the weight and the loss.

  

The chain rule lets us multiply the local derivatives:

  

```text

dLoss/dWeight

  

=

  

dLoss/dPrediction

×

dPrediction/dz

×

dz/dWeight

```

  

This is the mathematical foundation of the gradient calculation.

  

---

  

# 25. Weight Gradient

  

Our code:

  

```js

const gradient =

errorGradient *

tanhGradient *

inputs[i];

```

  

Each term has a meaning:

  

```text

errorGradient

↓

How does prediction affect loss?

  

tanhGradient

↓

How does z affect prediction?

  

inputs[i]

↓

How does weight affect z?

```

  

Together:

  

```text

weightGradient

=

errorGradient

×

tanhGradient

×

input

```

  

This tells us how much that particular weight contributes to the loss.

  

---

  

# 26. Why Is Input Part of the Weight Gradient?

  

Remember:

  

```text

z = input × weight + bias

```

  

Therefore:

  

```text

dz/dweight = input

```

  

Intuitively, a large input means a change in the weight has a larger effect.

  

Example:

  

```text

input = 10

```

  

Changing the weight by `0.1` changes `z` by:

  

```text

10 × 0.1 = 1

```

  

But:

  

```text

input = 0.1

```

  

Changing the weight by `0.1` changes `z` by:

  

```text

0.1 × 0.1 = 0.01

```

  

So the input naturally appears in the gradient.

  

---

  

# 27. Bias Gradient

  

We have:

  

```text

z = input × weight + bias

```

  

The derivative of `z` with respect to the bias is:

  

```text

dz/dbias = 1

```

  

Therefore:

  

```text

biasGradient

=

errorGradient

×

tanhGradient

```

  

In code:

  

```js

const biasGradient =

errorGradient * tanhGradient;

```

  

There is no input multiplication because the bias is added directly.

  

---

  

# 28. Updating the Weight

  

Once we calculate the gradient:

  

```js

const gradient =

errorGradient *

tanhGradient *

inputs[i];

```

  

we update:

  

```js

weights[i] -= learningRate * gradient;

```

  

Example:

  

```text

weight = 0.5

gradient = -0.4

learningRate = 0.1

```

  

Then:

  

```text

newWeight

=

0.5 - (0.1 × -0.4)

  

=

0.5 + 0.04

  

=

0.54

```

  

If the gradient is positive:

  

```text

weight = 0.5

gradient = 0.4

learningRate = 0.1

  

newWeight

=

0.5 - 0.04

  

=

0.46

```

  

So:

  

```text

gradient > 0

↓

decrease parameter

  

gradient < 0

↓

increase parameter

```

  

---

  

# 29. Updating the Bias

  

Similarly:

  

```js

bias -= learningRate * biasGradient;

```

  

The bias is another learnable parameter.

  

---

  

# 30. Complete Single-Neuron Training Code

  

The first complete training implementation looked like:

  

```js

function tanh(x) {

return Math.tanh(x);

}

  

function forward(inputs, weights, bias) {

let sum = bias;

  

for (let i = 0; i < inputs.length; i++) {

sum += inputs[i] * weights[i];

}

  

return tanh(sum);

}

  

function loss(prediction, target) {

return (prediction - target) ** 2;

}

  

const data = [

{ inputs: [1, 1], target: 1 },

{ inputs: [1, 0], target: 1 },

{ inputs: [0, 1], target: 1 },

{ inputs: [0, 0], target: -1 }

];

  

let weights = [

Math.random() * 2 - 1,

Math.random() * 2 - 1

];

  

let bias = Math.random() * 2 - 1;

  

const learningRate = 0.1;

  

for (let epoch = 0; epoch < 10000; epoch++) {

  

let totalLoss = 0;

  

for (const sample of data) {

  

const { inputs, target } = sample;

  

// Forward pass

const prediction =

forward(inputs, weights, bias);

  

// Loss

const error =

loss(prediction, target);

  

totalLoss += error;

  

// Gradients

const errorGradient =

2 * (prediction - target);

  

const tanhGradient =

1 - prediction ** 2;

  

// Update weights

for (let i = 0; i < weights.length; i++) {

  

const gradient =

errorGradient *

tanhGradient *

inputs[i];

  

weights[i] -=

learningRate * gradient;

}

  

// Update bias

const biasGradient =

errorGradient *

tanhGradient;

  

bias -=

learningRate * biasGradient;

}

  

if (epoch % 1000 === 0) {

console.log(

`Epoch ${epoch}, Loss: ${totalLoss}`

);

}

}

```

  

---

  

# 31. The Complete Training Process

  

The entire learning process can now be understood as:

  

```text

FORWARD PASS

↓

  

Input

↓

Weighted sum

↓

Bias

↓

tanh

↓

Prediction

↓

Loss

↓

  

BACKPROPAGATION

↓

  

Calculate error gradient

↓

Calculate activation gradient

↓

Apply chain rule

↓

Calculate parameter gradients

↓

Update weights and bias

↓

Repeat

```

  

This is the foundation of neural-network training.

  

---

  

# 32. What Is Backpropagation?

  

Backpropagation means calculating gradients by moving backward through the computation.

  

Forward:

  

```text

Input

↓

Hidden/Neuron

↓

Output

↓

Loss

```

  

Backward:

  

```text

Loss

↓

Output gradient

↓

Neuron gradients

↓

Weight gradients

```

  

The word "backpropagation" comes from propagating the error/gradient backward through the network.

  

---

  

# 33. Why Do We Need Multiple Neurons?

  

A single neuron can learn relatively simple relationships.

  

We then considered XOR:

  

```text

[0,0] → -1

[0,1] → 1

[1,0] → 1

[1,1] → -1

```

  

XOR means:

  

> Output 1 when the inputs are different; output -1 when they are the same.

  

The positive points are diagonally opposite:

  

```text

x₂

  

-1 +1

  

[0,0] [0,1]

  
  

[1,0] [1,1]

  

+1 -1

  

→ x₁

```

  

A single neuron computes:

  

```text

z = x₁w₁ + x₂w₂ + b

```

  

This creates a linear decision boundary.

  

A single straight line cannot separate the XOR pattern.

  

Therefore a single neuron cannot properly represent XOR.

  

This is an important reason we need multiple neurons and hidden layers.

  

---

  

# 34. Hidden Layers

  

Instead of:

  

```text

Input

↓

Neuron

↓

Output

```

  

we can create:

  

```text

Input

↓

Hidden Layer

↓

Output

```

  

For example:

  

```text

Hidden Layer

  

x₁ ───────→ Neuron 1 ───┐

│

x₂ ───────→ Neuron 2 ───┼──→ Output

│

...

```

  

Each hidden neuron can learn a different feature/transformation of the input.

  

The output neuron combines those features.

  

This lets the network represent more complex functions.

  

---

  

# 35. A Two-Layer Network

  

Our planned XOR network is:

  

```text

2 input values

↓

2 hidden neurons

↓

1 output neuron

```

  

Conceptually:

  

```text

Hidden Layer

┌─────────────┐

x₁ ───→│ Neuron 1 │───┐

└─────────────┘ │

├──→ Output

┌─────────────┐ │

x₂ ───→│ Neuron 2 │───┘

└─────────────┘

```

  

Each hidden neuron has:

  

```text

2 weights + 1 bias

```

  

There are two hidden neurons:

  

```text

2 × (2 weights + 1 bias)

= 6 parameters

```

  

The output neuron has:

  

```text

2 weights + 1 bias

= 3 parameters

```

  

Total:

  

```text

9 learnable parameters

```

  

---

  

# 36. Forward Propagation Through a Hidden Layer

  

For each hidden neuron:

  

```text

z = x₁w₁ + x₂w₂ + b

  

hiddenOutput = tanh(z)

```

  

For two hidden neurons:

  

```js

const hidden = [];

  

for (let i = 0; i < 2; i++) {

  

let sum = hiddenBiases[i];

  

for (let j = 0; j < 2; j++) {

sum += inputs[j] * hiddenWeights[i][j];

}

  

hidden.push(Math.tanh(sum));

}

```

  

Then the output neuron receives the hidden values:

  

```text

hidden[0]

hidden[1]

↓

weighted sum

↓

bias

↓

tanh

↓

output

```

  

Example:

  

```js

let outputSum = outputBias;

  

for (let i = 0; i < 2; i++) {

outputSum +=

hidden[i] * outputWeights[i];

}

  

const output = Math.tanh(outputSum);

```

  

---

  

# 37. Forward Pass in a Multi-Layer Network

  

The overall flow becomes:

  

```text

Input

↓

Hidden neuron 1 ──┐

│

Hidden neuron 2 ──┼──→ Output neuron

│

└──→ Prediction

```

  

Mathematically:

  

```text

Hidden = tanh(Input × HiddenWeights + HiddenBias)

  

Output =

tanh(Hidden × OutputWeights + OutputBias)

```

  

The important change is that the output layer is now receiving **features created by the hidden layer**.

  

---

  

# 38. Backpropagation Becomes More Interesting

  

For a single neuron:

  

```text

Loss

↓

Output

↓

Weight

```

  

With multiple layers:

  

```text

Loss

↓

Output

↓

Output weights

↓

Hidden neurons

↓

Hidden weights

```

  

The gradients must travel backward through every operation.

  

This is the real meaning of backpropagation in deeper networks.

  

---

  

# 39. Important Terminology

  

| Term | Meaning |

|---|---|

| Neuron | Computes weighted inputs + bias + activation |

| Weight | Learnable parameter controlling input influence |

| Bias | Learnable offset |

| Activation | Nonlinear function applied to neuron output |

| `tanh` | Activation that maps values toward -1 to +1 |

| Forward pass | Computing a prediction from input |

| Prediction | Model's output |

| Target | Correct expected output |

| Loss | Numerical measurement of prediction error |

| Total loss | Sum of losses across examples |

| MSE | Mean of squared errors |

| Gradient | Direction/sensitivity of loss with respect to a parameter |

| Gradient descent | Updating parameters to reduce loss |

| Learning rate | Size of each parameter update |

| Backpropagation | Computing gradients backward through the network |

| Epoch | One complete pass through the training data |

| Hidden layer | Layer between input and output |

| Parameter | A value learned during training |

  

---

  

# 40. The Most Important Mental Model

  

Do not memorize the gradient equations as isolated formulas.

  

Think of the network as a chain of operations.

  

For our neuron:

  

```text

x

↓

× w

↓

+ b

↓

tanh

↓

prediction

↓

loss

```

  

During the forward pass:

  

```text

Input → prediction → loss

```

  

During backpropagation:

  

```text

loss → gradients → parameter updates

```

  

The chain rule tells us how the effect flows backward through each operation.

  

---

  

# 41. The Three Questions to Ask When Looking at a Gradient

  

Whenever you see a gradient, ask:

  

### Question 1

  

> What quantity am I differentiating?

  

Usually:

  

```text

Loss

```

  

### Question 2

  

> What parameter am I trying to update?

  

For example:

  

```text

weight

bias

```

  

### Question 3

  

> What operations are between the parameter and the loss?

  

Then use the chain rule to multiply the local derivatives.

  

---

  

# 42. What We Have Built So Far

  

We have conceptually built:

  

```text

Random weights

↓

Single neuron

↓

tanh activation

↓

Prediction

↓

Squared loss

↓

Gradient calculation

↓

Backpropagation

↓

Gradient descent

↓

Updated weights

↓

Repeat

```

  

This is a genuine learning neural network, even though it is extremely small.

  

---

  

# 43. What Comes Next

  

The next implementation should be a proper multi-layer neural network that can solve XOR.

  

We will implement:

  

```text

2 inputs

↓

2+ hidden neurons

↓

1 output neuron

```

  

Then learn:

  

```text

[0,0] → -1

[0,1] → 1

[1,0] → 1

[1,1] → -1

```

  

The important new concepts will be:

  

- matrices

- matrix multiplication

- hidden-layer gradients

- backpropagation through multiple layers

- parameter organization

- training a complete neural network

  

After that we can start moving toward language modeling:

  

```text

characters

↓

token IDs

↓

embeddings

↓

neural network

↓

next-token prediction

```

  

Eventually:

  

```text

Tokenization

↓

Embeddings

↓

Self-attention

↓

Transformer block

↓

Language model

```

  

---

  

# 44. Key Takeaways So Far

  

1. A neuron computes a weighted sum plus a bias.

2. Weights and biases are learnable parameters.

3. An activation function introduces nonlinearity.

4. `tanh` maps values toward the range `-1` to `1`.

5. A forward pass produces a prediction.

6. A loss function measures how wrong the prediction is.

7. Squared error makes errors positive and penalizes large mistakes more strongly.

8. Total loss summarizes performance across the dataset.

9. A gradient tells us how changing a parameter affects the loss.

10. The chain rule lets us calculate gradients through multiple operations.

11. Gradient descent updates parameters in the direction that reduces loss.

12. Learning rate controls the size of parameter updates.

13. Backpropagation computes gradients backward through the network.

14. A single neuron cannot represent every function, such as XOR.

15. Hidden layers allow networks to learn combinations of intermediate features.

16. The same fundamental training loop will appear again when we build our tiny language model.

  

---

  

# 45. Current Project Structure

  

At this stage:

  

```text

mini_ai/

│

├── src/

│ ├── neuron.js

│ └── network.js

│

└── package.json

```

  

We started with:

  

```text

src/neuron.js

```

  

and will use:

  

```text

src/network.js

```

  

for the multi-layer network.

  

The project is intentionally being built without ML frameworks so that the underlying mathematics and algorithms are visible.