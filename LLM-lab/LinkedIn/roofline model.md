
related to the note: [[Throughput Ceiling & Latency Knee and why blindly increasing batch size is a bad serving strategy.]]

![](https://i.imgur.com/sawwZSD.png)
Figure 6 presents its roofline characterization across increasing
active-context lengths for Mistral-7B and Granite-8B. The
roofline model relates kernel performance to arithmetic in-
tensity (AI), defined as the number of arithmetic operations
performed per byte transferred from DRAM. Kernels with low
AI operate in the memory-bound regime (red), where perfor-
mance is limited by memory bandwidth, whereas kernels with
high AI operate in the compute-bound regime (green), where
performance is limited by computational throughput.The
roofline boundaries are defined by the device’s peak single-
precision compute throughput and peak DRAM bandwidth,
representing the accelerator’s theoretical maximum compute
performance and memory-transfer capability, respectively

 As
shown in Figure 6, the attention kernel remains in the memory-
bound regime across all evaluated configurations, with arith-
metic intensity varying only marginally as the active context
increases.  Furthermore, GPU compute capacity also remains
largely underutilized. Across all evaluated configurations, the
kernel sustains less than 6 TFLOP/s, more than two orders
of magnitude below the hardware’s theoretical peak Tensor
Core throughput of 989 TFLOP/s. These results demonstrate
that increasing the active-context size —whether by increasing
batch size, input length, or output length—does not shift the
attention kernel toward the compute-bound regime, because
the additional computation is accompanied by a proportional
increase in DRAM traffic.
Accordingly, as the active context increases, the attention
kernel follows an almost vertical trajectory in the roofline
model, reflecting its nearly constant arithmetic intensity while
performance approaches the DRAM-bandwidth ceiling. Mem-
ory bandwidth therefore becomes the limiting resource, pre-
venting further performance gains once saturation is reached.