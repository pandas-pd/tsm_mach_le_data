# PyTorch Dense Encoder — How It Works (vibe written by Claude)

## Overview

A dense encoder is a stack of fully connected (linear) layers that progressively compresses an input vector into a smaller **latent representation**. In the context of a movie recommendation system, it takes a high-dimensional feature vector (genres, actors, director, duration, rating) and squeezes it into a compact embedding that captures the most important information.

```
[500 features] → Linear → [256] → Linear → [128] → Linear → [32 latent]
```

---

## Building Blocks

### `nn.Linear` — the core layer

A linear layer performs a matrix multiplication plus a bias addition:

```
output = input @ W.T + b
```

- `W` is a weight matrix — a learnable parameter
- `b` is a bias vector — also learnable
- Example: `nn.Linear(256, 128)` learns a W of shape (128, 256)

This is what actually "learns" during training — the weights are adjusted via backpropagation to minimize the loss.

### `nn.ReLU` — activation function

Without activations, stacking linear layers collapses into a single linear transformation. ReLU adds **non-linearity**, enabling the network to learn complex patterns:

```
ReLU(x) = max(0, x)
```

Negative values become 0, positive values pass through unchanged. This lets the network decide which signals are relevant and which to suppress.

### `nn.BatchNorm1d` — batch normalization

Normalizes the output of a layer across the batch, keeping values in a stable range (mean ≈ 0, std ≈ 1). This prevents values from exploding or vanishing as they pass through many layers, making training significantly more stable.

### `nn.Dropout` — regularization

Randomly zeros out a fraction of neurons during training:

```python
nn.Dropout(0.3)  # disables 30% of neurons randomly each forward pass
```

This prevents the network from over-relying on any single neuron, reducing overfitting. During inference (`model.eval()`), dropout is automatically disabled.

### `nn.Sequential` — layer container

Chains layers together and calls them in order. The output of each layer becomes the input of the next.

---

## The Full Encoder

```python
self.encoder = nn.Sequential(
    nn.Linear(input_dim, 256),   # compress input features → 256
    nn.BatchNorm1d(256),          # stabilize
    nn.ReLU(),                    # non-linearity
    nn.Dropout(0.3),              # regularize

    nn.Linear(256, 128),          # compress further → 128
    nn.BatchNorm1d(128),
    nn.ReLU(),
    nn.Dropout(0.2),

    nn.Linear(128, latent_dim)    # final compression → 32
)
```

---

## Forward Pass — Step by Step

Given a single movie's feature vector (e.g. 500-dimensional):

```python
x = tensor([1.0, 0.0, 1.0, 0.0, 0.8, 0.5, ...])  # 500 features

# Layer 1
x = W1 @ x + b1   # Linear:    500 → 256
x = BatchNorm(x)   # Normalize: stabilize values
x = ReLU(x)        # Activate:  zero out negatives
x = Dropout(x)     # Regularize: randomly zero some out

# Layer 2
x = W2 @ x + b2   # Linear:    256 → 128
x = BatchNorm(x)
x = ReLU(x)
x = Dropout(x)

# Layer 3
x = W3 @ x + b3   # Linear:    128 → 32

# Result: a 32-dimensional embedding
latent = tensor([0.83, -0.21, 0.45, ...])  # 32 values
```

---

## Why Does It Work?

The encoder is one half of an **autoencoder** — it is trained alongside a decoder that reconstructs the original input from the latent vector. To reconstruct well, the encoder is forced to pack the most important information into those 32 numbers.

As a result, movies with similar characteristics end up with similar latent vectors:

```
Inception     → encoder → [0.8,  0.2, -0.5, ...]  ← similar
Interstellar  → encoder → [0.7,  0.3, -0.4, ...]  ←

The Hangover  → encoder → [-0.6, 0.9,  0.1, ...]  ← very different
```

This makes the latent vectors ideal for **cosine similarity** — similar movies cluster together in latent space naturally, without any manual feature engineering.

---

## Key Takeaway

| Layer | Purpose |
|---|---|
| `nn.Linear` | Learn a compressed representation (the actual learning) |
| `nn.ReLU` | Enable non-linear patterns to be learned |
| `nn.BatchNorm1d` | Keep training stable |
| `nn.Dropout` | Prevent overfitting |
| `nn.Sequential` | Chain everything together cleanly |

The deeper the network and the smaller the latent dimension, the more the model is forced to distill only the essential structure of the data.
