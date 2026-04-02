## 4.1 Birth Process

A process is called a birth process if it is characterized by the appearance of an individual within a population according to a certain law. The most common example in everyday life is the arrival of cars at a traffic light intersection or a person at a counter.

The Poisson birth process is an absolute Markovian birth process in which the probability of a birth in a small time interval is independent of the population size.

![](https://i.imgur.com/2pYgu5b.png)
#### explanation
![](https://i.imgur.com/12f5He2.png)
![](https://i.imgur.com/sSa83Sx.png)
![](https://i.imgur.com/MMj3hUg.png)
![](https://i.imgur.com/4eb3MJe.png)
![](https://i.imgur.com/XLC6QSG.png)
#### Example:
![](https://i.imgur.com/VoXwly9.png)
![](https://i.imgur.com/zYgD9JE.png)
![](https://i.imgur.com/o9KV5p3.png)



## 4.2 Poisson Death Process

The Poisson death process is an absolute Markovian death process in which the probability of a death in a small time interval is independent of the population size.
![](https://i.imgur.com/DMwCiwN.png)
#### Explanation
![](https://i.imgur.com/jjVqZrk.png)
![](https://i.imgur.com/fBLT6yt.png)
![](https://i.imgur.com/4HWSGos.png)
##### Example:
![](https://i.imgur.com/STkBu3t.png)
![](https://i.imgur.com/LdLRlrA.png)
# 4.3 Birth and Death Process

An important class of Markov processes, which appears in the study of waiting phenomena, is that of birth and death processes.

For example: a queue at a traffic light intersection, where the states of the system are the number of cars in the service area. The arrival of cars can be considered a birth, and the departure of cars can be considered a death.
![](https://i.imgur.com/tv8yaLh.png)
![](https://i.imgur.com/F7t0plh.png)
![](https://i.imgur.com/zIeSIFi.png)
![](https://i.imgur.com/8MmwnUU.png)
Another equivalent way to define the process is to introduce the events and their probabilities as follows:
![](https://i.imgur.com/yK7zAWU.png)
![](https://i.imgur.com/VRZW2hb.png)
![](https://i.imgur.com/5TMxvGw.png)
![](https://i.imgur.com/MgAc6tB.png)
![](https://i.imgur.com/tmblo4Z.png)
![](https://i.imgur.com/Y9m7W3k.png)
![](https://i.imgur.com/dogresg.png)
##### explanation
![](https://i.imgur.com/mRiEyet.png)
![](https://i.imgur.com/su2urCQ.png)
![](https://i.imgur.com/3CFgZoj.png)
![](https://i.imgur.com/mThXK1Y.png)
![](https://i.imgur.com/JUK6oqf.png)
❖ Transition Diagram and Intuitive Interpretation
![](https://i.imgur.com/hpBw5Kn.png)
![](https://i.imgur.com/cdWftEK.png)
![](https://i.imgur.com/ZyCUaaY.png)
![](https://i.imgur.com/eBb5uWN.png)
# ❖ Birth and Death Process with Two States

### Example:
![](https://i.imgur.com/yIqqSKK.png)
![](https://i.imgur.com/oVlugg5.png)
![](https://i.imgur.com/9dHlxTX.png)
![](https://i.imgur.com/tOSoLyB.png)
![](https://i.imgur.com/MpB0Zxx.png)
gpt solution
![](https://i.imgur.com/NNS39I7.png)
![](https://i.imgur.com/05mogey.png)
![](https://i.imgur.com/SZiSSch.png)
![](https://i.imgur.com/OTiwYtC.png)
![](https://i.imgur.com/4y7zd8x.png)


# Steady State Regime
![](https://i.imgur.com/ZWfwh2s.png)
![](https://i.imgur.com/uIpEH6G.png)
![](https://i.imgur.com/SaX0anU.png)
![](https://i.imgur.com/kFQVeZe.png)


explanation
![](https://i.imgur.com/6fYXukV.png)
This is why the derivatives of the state probabilities become zero.
![](https://i.imgur.com/ZsJ7Ziw.png)
![](https://i.imgur.com/KInJxgG.png)
![](https://i.imgur.com/ACALQwm.png)
![](https://i.imgur.com/dwAxOuz.png)
![](https://i.imgur.com/iq0Ql4E.png)
![](https://i.imgur.com/w1bpyVP.png)
![](https://i.imgur.com/0MMH8N5.png)
![](https://i.imgur.com/nQzK10d.png)


# 1. Arrival Process
![](https://i.imgur.com/j1dcmxq.png)
![](https://i.imgur.com/vSiQgB2.png)
# 2. Service Time
![](https://i.imgur.com/sSRNsp3.png)
![](https://i.imgur.com/Obik2B0.png)
![](https://i.imgur.com/hqaAKhY.png)


# 3. Structure and Discipline of the Queue
## 3.1 Number of Servers

![](https://i.imgur.com/GpuqfK6.png)
![](https://i.imgur.com/Lf86loZ.png)

## 3.2 Queue Capacity
![](https://i.imgur.com/4UHXbBr.png)
![](https://i.imgur.com/5jEOzLf.png)
![](https://i.imgur.com/qTrGITV.png)


The **service discipline** determines **which waiting customer is served next** when a server becomes available.
The most common disciplines are:

- FIFO (first in, first out) or FCFS (first come, first served): this is the standard queue where customers are served in their order of arrival.
- LIFO (last in, first out) or LCFS (last come, first served). This corresponds to a stack, where the last customer arrived (placed on the stack) will be the first served (removed from the stack).
- RANDOM (random): The next customer to be served is chosen randomly from the queue.


Corollary: Five characteristics allow describing the behavior of a queue:

1. The customer arrival process;
2. The service process;
3. The queue service discipline, i.e., the way customers are selected to receive their service;
4. The system capacity, i.e., the total number of customers that can be in the system at any given instant.
5. The number of servers.


# ❖ General Structure of an M/M/1 System
![](https://i.imgur.com/AI8Mz5W.png)
For an M/M/1 queue to reach equilibrium, λ<μ  is required (otherwise the queue size will grow to infinity).
![](https://i.imgur.com/h2O1kqs.png)
![](https://i.imgur.com/Hzmit2h.png)
![](https://i.imgur.com/5qMrbEG.png)
![](https://i.imgur.com/Tn3lyy8.png)
![](https://i.imgur.com/xFanTe5.png)
![](https://i.imgur.com/wUXcWWU.png)
![](https://i.imgur.com/QNKCnPd.png)
### ➤ Steady-State Analysis
![](https://i.imgur.com/z7TT4q8.png)
<mark>skip</mark>
![](https://i.imgur.com/ylWMJ5k.png)
![](https://i.imgur.com/8aUJIWS.png)
explaination

----
![](https://i.imgur.com/9xgle5n.png)
![](https://i.imgur.com/PuvJw22.png)

---

![](https://i.imgur.com/iy0x7D4.png)
![](https://i.imgur.com/mBazFeK.png)
![](https://i.imgur.com/dWtvMpk.png)
![](https://i.imgur.com/WK1PFo4.png)
![](https://i.imgur.com/v3xd11F.png)
![](https://i.imgur.com/juMlRqK.png)



# Performance Measures of an M/M/1 Queueing System
![](https://i.imgur.com/ALbTeQF.png)
![](https://i.imgur.com/445ArOl.png)
### Average Number of Customers in the System
![](https://i.imgur.com/y9dntXq.png)
![](https://i.imgur.com/YvEECra.png)
## Average Number of Customers in the Queue:
![](https://i.imgur.com/GXNOKg0.png)
## Average Time in the System:
![](https://i.imgur.com/DdVSTOA.png)

## Average Waiting Time in the Queue:
![](https://i.imgur.com/68LJwDL.png)
![](https://i.imgur.com/bFxyRlQ.png)
