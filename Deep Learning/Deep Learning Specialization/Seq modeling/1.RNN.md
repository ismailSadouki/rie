![](https://i.imgur.com/wb3AkKc.png)

calculations
![](https://i.imgur.com/xsNgrz6.png)
![](https://i.imgur.com/agjhHCq.png)

#### From MIT
our forward pass
![](https://i.imgur.com/By2utWi.png)


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
hidden rnn unit is like this
![](https://i.imgur.com/sxirn4P.png)
![](https://i.imgur.com/QJ6h1is.png)
![](https://i.imgur.com/c2wFui1.png)

![](https://i.imgur.com/q28VdbD.png)

![](https://i.imgur.com/qYWjNMP.png)
GRU designed to improve the handling of long-range dependencies and mitigate vanishing gradient issues.
Understanding GRUs

- GRUs introduce a memory cell (C) that retains information, allowing the model to remember important details, such as whether a subject is singular or plural.
- The output activation (a) is equal to the memory cell value (C), simplifying the relationship between the two.
Key Components of GRUs

- The GRU uses a candidate value (C~) for updating the memory cell, computed through a tanh activation function.
- An update gate (Γu) determines when to update the memory cell, allowing the model to maintain or change its memory based on the input.
Implementation and Functionality

- The GRU can handle multiple dimensions in its memory cell, allowing selective updates to different bits of information.
- The design of GRUs has been refined through research, making them effective for various applications, including natural language processing.

Overall, GRUs are a robust solution for capturing long-range dependencies in sequence data, paving the way for more effective RNN architectures.

---

# LSTM
![](https://i.imgur.com/SsCFsCE.png)
![](https://i.imgur.com/w6nIB9A.png)
![](https://i.imgur.com/yIe7EQG.png)
advanced components used in sequence modeling, particularly in comparison to Gated Recurrent Units (GRUs).
LSTM Overview

- LSTMs are designed to learn long-range dependencies in sequences, making them more powerful than GRUs.
- They consist of three gates: the update gate, the forget gate, and the output gate, which manage the flow of information.
Key Equations and Structure

- The equations governing LSTMs differ from GRUs, with separate gates controlling updates and forget operations.
- The memory cell can retain old values while incorporating new information, allowing for better memory retention over time.

Applications and Variations

- LSTMs can be enhanced with peephole connections, where gate values depend on the previous memory cell value.
- While LSTMs are generally more powerful, GRUs are simpler and may be preferred for larger models due to faster computation.


---
# Bidirectional RNN
![](https://i.imgur.com/9cQ6pB8.png)
Bidirectional RNN

- A BRNN allows information to flow from both past and future inputs, enhancing the model's ability to make predictions based on the entire sequence.
- This is particularly useful in tasks like named entity recognition, where context from both directions is necessary to determine the meaning of words.
Applications and Limitations

- BRNNs are effective for many natural language processing tasks, especially when the entire input sequence is available.
- However, they require the complete sequence before making predictions, which can be a limitation in real-time applications like speech recognition.
---
# Deep RNNs
![](https://i.imgur.com/vxkGXpm.png)
