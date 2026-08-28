


I thought I broke my KV cache.

I'm building a small LLM inference engine and was testing the numerical difference between cached and uncached inference.

I first compared the final logits and noticed a difference.

I was about to move on because the output token was still the same.

But something caught my attention.

The difference wasn't appearing all at once.

When I compared the hidden states layer by layer, I could see the numerical difference starting small and then getting larger as it propagated through the transformer.

That made me curious:

**Where does the difference actually start?**

So instead of trying to debug the whole model at once, I ignored the later layers and focused only on **layer 0**.

I traced the computation step by step:

```text
K / V cache
      ↓
GQA
      ↓
Attention
      ↓
O projection
      ↓
Residual
      ↓
MLP input
      ↓
Gate projection
      ↓
Up projection
      ↓
SwiGLU
      ↓
Down projection
      ↓
MLP output
```

And this is where it got interesting.

For layer 0, almost everything matched exactly:

```text
V                         → 0.0
GQA                       → 0.0
Attention context         → 0.0
O projection              → 0.0
Pre-MLP residual          → 0.0
MLP input                 → 0.0
Up projection             → 0.0
SwiGLU                    → 0.0
Down projection           → 0.0
```

The gate projection had only:

```text
MAX DIFF : 4.77e-7
```

So the KV cache and attention path were looking pretty healthy.

Then I found something confusing.

The actual MLP output differed by:

```text
MAX DIFF : 0.00390625
```

My first thought was:

"Okay, maybe the problem is inside the MLP."

So I isolated it.

I took the exact same final-token MLP input and ran the MLP directly.

The result?

**Exactly identical.**

So the MLP itself wasn't producing a difference when given the same input.

One interesting detail was that the two paths were executed with different sequence shapes.

Cached decoding processes only the new token:

```text
[1, 1, 896]
```

while uncached inference processes the whole sequence:

```text
[1, 6, 896]
```

and we take the final token afterward.

That was a clue, but not enough to say, "this is definitely the bug."

So I changed another variable.

I ran the same cached-vs-uncached experiment in FP32.

The final logit difference went from:

```text
BF16:
MAX DIFF = 1.74e-1
```

to:

```text
FP32:
MAX DIFF = 1.24e-5
```

And the predicted token was still identical.

At this point, the evidence was pointing away from a logical KV-cache bug and much more toward numerical differences caused by the different execution paths, especially with BF16.

And this makes sense at a lower level too.

Floating-point operations are not generally associative:

```text
(a + b) + c ≠ a + (b + c)
```

So changing the order or way operations are executed can produce small numerical differences.

And in a deep transformer, a tiny difference doesn't necessarily stay tiny.

It can propagate through layer after layer.

What I liked most about this debugging process is that the final number alone didn't tell me much.

"Logits differ by 0.17" doesn't tell you where the problem is.

But seeing the difference **start small and accumulate across layers** gave me a direction.

It changed the question from:

> "Why are my logits different?"

to:

> "Where do the two computations first stop being equivalent?"

And that led me to inspect the model one operation at a time.

That's probably one of the best things about debugging.

You don't just fix the bug.

You end up understanding **why the system works**.

You learn what tensors actually look like, where they come from, how information flows through the model, and what assumptions each component makes.

Debugging isn't just fixing things.

It's a way of learning your field from the inside.


![](https://i.imgur.com/0iyBdb0.png)
![](https://i.imgur.com/zuJ1cuD.png)
![](https://i.imgur.com/EUOgxdr.png)
![](https://i.imgur.com/YsJTmRw.png)
![](https://i.imgur.com/P6ZGS02.png)
![](https://i.imgur.com/ZUVvwmU.png)
![](https://i.imgur.com/4MjpHyr.png)
![](https://i.imgur.com/tDQCWu4.png)
![](https://i.imgur.com/R2Z2VZM.png)
![](https://i.imgur.com/IYSlFhs.png)
![](https://i.imgur.com/zH7aIfM.png)
