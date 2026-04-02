GELU and SwiGLU are more complex and smooth activation functions incorpo-
rating Gaussian and sigmoid-gated linear units, respectively. They offer improved performance for deep learning models, unlike the simpler ReLU.
![](https://i.imgur.com/Sl9Q6i1.png)
```python
class GELU(nn.Module):
def __init__(self):
super().__init__()
def forward(self, x):
return 0.5 * x * (1 + torch.tanh(
torch.sqrt(torch.tensor(2.0 / torch.pi)) *
(x + 0.044715 * torch.pow(x, 3))
))
```
![](https://i.imgur.com/5wGXvTt.png)

GELU (left) is a smooth, nonlinear function that approximates ReLU but with a non-
zero gradient for almost all negative values (except at approximately x = –0.75).

Next, let’s use the GELU function to implement the small neural network module,
FeedForward, that we will be using in the LLM’s transformer block later.
```python
class FeedForward(nn.Module):

def __init__(self, cfg):

super().__init__()

self.layers = nn.Sequential(

nn.Linear(cfg["emb_dim"], 4 * cfg["emb_dim"]),

GELU(),

nn.Linear(4 * cfg["emb_dim"], cfg["emb_dim"]),

)

def forward(self, x):

return self.layers(x)
```
In the 124-million-parameter GPT
model, it receives the input batches with tokens that have an embedding size of 768 each via the GPT_CONFIG_124M dictionary where GPT_CONFIG_ 124M["emb_dim"] = 768.
![](https://i.imgur.com/2FPfBCH.png)
Following the example in figure 4.9, let’s initialize a new FeedForward module with a token embedding size of 768 and feed it a batch input with two samples and three tokens each:
![](https://i.imgur.com/GL5gZfk.png)
The FeedForward module plays a crucial role in enhancing the model’s ability to learn from and generalize the data. Although the input and output dimensions of this module are the same, it internally expands the embedding dimension into a higherdimensional space through the first linear layer, as illustrated in figure 4.10. This expan- sion is followed by a nonlinear GELU activation and then a contraction back to the orig- inal dimension with the second linear transformation. <mark>Such a design allows for the exploration of a richer representation space</mark>.
![](https://i.imgur.com/CCNHGwQ.png)
Moreover, the uniformity in input and output dimensions simplifies the architecture
by enabling the stacking of multiple layers, as we will do later, without the need to
adjust dimensions between them, thus making the model more scalable.
![](https://i.imgur.com/ig8NPzZ.png)
