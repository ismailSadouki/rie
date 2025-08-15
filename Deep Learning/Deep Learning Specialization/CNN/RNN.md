![](https://i.imgur.com/wb3AkKc.png)

calculations
![](https://i.imgur.com/xsNgrz6.png)
![](https://i.imgur.com/agjhHCq.png)
# Backpropagation Through Time
![](https://i.imgur.com/84ryeOt.png)

# Different Types of RNNs
![](https://i.imgur.com/hhuPSOI.png)

# Language Model and Sequence Generation
![](https://i.imgur.com/VawA8ls.png)

---
# Sampling Novel Sequences
---
# Vanishing Gradients with RNNs
- RNNs struggle with long-term dependencies in sequences, making it difficult to remember information from earlier inputs when generating later outputs.
- The vanishing gradient problem occurs during backpropagation, where gradients diminish as they propagate back through many layers, hindering learning.
Gradient Issues in RNNs

- Exploding gradients can also occur, leading to numerical overflow and unstable training, but they are easier to detect and can be managed with gradient clipping.
- In contrast, vanishing gradients are more challenging to address and will be explored in subsequent videos.

# Gated Recurrent Unit (GRU)
