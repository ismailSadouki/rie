
Nice — **FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness** is a very important paper if you're working toward LLM inference/serving and GPU systems.

Given what you've been studying around **KV cache, inference engines, and vLLM**, I’d recommend reading it less as “an attention optimization trick” and more as:

> **How can we compute exactly the same attention while dramatically reducing GPU memory traffic?**

The key mathematical object is still standard attention:

[  
Q,K,V\in\mathbb{R}^{N\times d}  
]

and

[  
S = QK^\top \in \mathbb{R}^{N\times N}  
]

[  
P = \operatorname{softmax}(S)\in\mathbb{R}^{N\times N}  
]

[  
O = PV\in\mathbb{R}^{N\times d}.  
]

The problem is that (S) and (P) are both (N\times N). For long sequences, that becomes enormous.

### What FlashAttention changes

It **doesn't change the mathematical result**:

[  
O=\operatorname{softmax}(QK^\top)V.  
]

Instead, it changes **how the computation is scheduled on the GPU**.

Rather than:

```text
Q,K
 ↓
QKᵀ
 ↓
N×N matrix in HBM
 ↓
softmax
 ↓
N×N matrix in HBM
 ↓
PV
 ↓
O
```

FlashAttention divides (Q,K,V) into blocks and performs the computation while keeping intermediate values in **fast on-chip SRAM/registers** as much as possible.

The important distinction is:

[  
\boxed{\text{same FLOPs / same attention result}}  
]

but

[  
\boxed{\text{much less HBM ↔ SRAM data movement}}.  
]

That's the **IO-awareness** part.

### The concept you should focus on while reading

Don't initially get lost in the CUDA/block-size details. Make sure you understand these four things:

1. **Why standard attention is memory expensive**  
    [  
    O(N^2)  
    ]  
    intermediate storage.
    
2. **GPU memory hierarchy**
    
    Roughly:
    
    [  
    \text{HBM} \gg \text{SRAM/registers}  
    ]
    
    in capacity, while SRAM/registers provide much higher bandwidth / lower latency.
    
3. **Tiling**
    
    Instead of computing the whole
    
    [  
    QK^\top\in\mathbb{R}^{N\times N},  
    ]
    
    compute blocks such as
    
    [  
    Q_iK_j^\top  
    ]
    
    where
    
    [  
    Q_i\in\mathbb{R}^{B_r\times d},  
    \qquad  
    K_j\in\mathbb{R}^{B_c\times d}.  
    ]
    
    Therefore,
    
    [  
    Q_iK_j^\top  
    \in\mathbb{R}^{B_r\times B_c}.  
    ]
    
4. **Online softmax**
    
    This is the clever mathematical part.
    
    You cannot simply compute softmax independently on each (K_j) block because:
    
    [  
    \operatorname{softmax}(QK^\top)  
    ]
    
    requires normalization across **all (N) keys**.
    
    FlashAttention therefore maintains running statistics—essentially a running maximum and normalization factor—so it can process blocks sequentially while producing the **exact same softmax**.
    

---

If you're going through the paper **right now**, send me the paragraph/equation/section you're stuck on, and I'll explain it mathematically with the **exact tensor shapes at every step**, including what resides in HBM vs SRAM and why the algorithm is correct.