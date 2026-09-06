
# 🚀 LLM Inference Isn't Just About How Fast One Request Runs

While working on my **mini inference engine** and moving into **vLLM serving**, I started looking at a different question:

> **What happens when more users hit the model at the same time?**

A benchmark with only one concurrent request can be misleading.

At low concurrency, adding more users can actually improve **throughput**, because the GPU has more work available and can utilize its resources more efficiently.

But as concurrency keeps increasing, the system eventually approaches its capacity.


![](https://i.imgur.com/cwT5gcT.png)


---

## 📈 Throughput Ceiling

The **throughput ceiling** is the point where increasing concurrency no longer produces significant throughput improvements.  commonly
referred to as the throughput plateau.

For example:

```text
1 user   → 10 req/s
4 users  → 20 req/s
8 users  → 28 req/s
16 users → 30 req/s
32 users → 30.5 req/s
```

The system is approaching its throughput ceiling.

Adding more users doesn't give us much more throughput.

Instead, requests increasingly have to **wait for compute resources**.

So we can think of it as:

```text
Concurrency ↑
      ↓
Throughput ↑
      ↓
GPU utilization ↑
      ↓
Saturation
      ↓
Throughput plateaus
```

The important point is that **more concurrency does not mean infinite throughput**.

Eventually, the hardware becomes the bottleneck.




> note: The saturation cannot be explained by batch size alone. The underlying cause is the memory behavior of decode attention, particularly as active context length increases.

```
 through-
put plateau is fundamentally caused by DRAM-bandwidth
saturation in the attention kernels, resulting from excessive
memory traffic during the decode phase. Furthermore, we
show that this limitation is governed not only by batch size
but also by the active context, defined as the total number of
tokens involved at each processing step

SLIM paper: https://arxiv.org/pdf/2607.29575
```


---

## 🦵 Latency Knee

The **latency knee** is slightly different.

It is the point where latency starts increasing **much more rapidly** as the system approaches saturation.

Imagine:

```text
Latency
   ↑
   │                              /
   │                           /
   │                       /
   │                  ____/
   │             ____/
   │___________/
   └──────────────────────────────→ Concurrency
                         ↑
                    latency knee
```

Before the knee, adding users may only increase latency gradually.

After the knee, a small increase in concurrency can cause a large increase in latency.

For an interactive LLM application, this matters a lot.

A system might process more requests per second, but if users are waiting significantly longer for responses, the **user experience is getting worse**.



---

# 🧪 My vLLM Experiment

I finished my first concurrency ramp using **Locust** against an OpenAI-compatible vLLM endpoint.

I tested:

```text
1 → 4 → 8 → 16 → 32 concurrent users
```

The results:

|Concurrent users|Requests|Failures|Avg latency|p50|p95|p99|Throughput|
|--:|--:|--:|--:|--:|--:|--:|--:|
|1|80|0|221 ms|220 ms|230 ms|270 ms|2.71 req/s|
|4|286|0|264 ms|270 ms|290 ms|340 ms|9.61 req/s|
|8|543|0|286 ms|290 ms|310 ms|400 ms|18.28 req/s|
|16|969|0|341 ms|340 ms|370 ms|480 ms|32.58 req/s|
|32|1520|0|471 ms|470 ms|540 ms|660 ms|50.98 req/s|

And this is where it gets interesting.

---

## 🔍 What Do The Results Tell Me?

Throughput kept increasing:

```text
2.71
  ↓
9.61
  ↓
18.28
  ↓
32.58
  ↓
50.98 req/s
```

So I **haven't reached a clear throughput ceiling yet**.

At 32 concurrent users, the server is still processing substantially more requests per second.

But latency tells a different story.

Average latency:

```text
221 ms → 264 ms → 286 ms → 341 ms → 471 ms
```

The jump from **16 → 32 users** is particularly noticeable:

```text
341 ms → 471 ms
```

That's roughly a **38% increase in average latency**.

And p95 latency goes:

```text
480 ms → 660 ms
```

while throughput increases:

```text
32.58 → 50.98 req/s
```

So I'm getting **more throughput**, but I'm also paying for it with significantly higher latency.

That's the beginning of the behavior I'm looking for when studying **serving under load**.

I wouldn't claim that 32 users is the saturation point yet.

Instead:

> **The throughput ceiling hasn't been reached, but the latency curve is beginning to bend upward more sharply.**

That's exactly why a concurrency ramp is more informative than running a single benchmark.

---

# 🎯 The Bigger Lesson

This experiment changed how I think about LLM inference.

Instead of asking only:

> **"How many tokens/sec can my model generate?"**

I'm now asking:

> **"How does the entire serving system behave as concurrent demand increases?"**

Because maximum throughput isn't necessarily the same thing as a **good serving point**.

You can keep pushing concurrency and get more throughput, but eventually the additional throughput comes with rapidly increasing latency.

For an interactive application, that trade-off matters.

---

# 🏗️ What's Actually Happening Behind The Request?

This is also why serving systems like **vLLM** are much more interesting than simply calling:

```python
model.generate(...)
```

There is an entire system behind the request:

```text
HTTP Request
     ↓
Scheduler
     ↓
Batching
     ↓
KV Cache
     ↓
GPU
     ↓
Sampling
     ↓
HTTP Response
```

And as concurrency increases, the serving system has to continuously manage all of these resources.

That's the part I'm currently digging into. 🔥

My next step is to push the concurrency further and see where **throughput actually plateaus** and the **latency knee becomes more obvious**.

#LLM #Inference #vLLM #MachineLearning #AI #GPU #LLMOps #DeepLearning #Systems