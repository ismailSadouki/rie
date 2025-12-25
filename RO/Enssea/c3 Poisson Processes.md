# 3.1 Counting Process
A stochastic process {N(t), t ≥ 0} is called a counting process if it satisfies the following properties:

1. N(0) = 0
2. For all t ≥ 0, the number N(t) takes positive integer values.
3. The function N(t) is increasing with time t.
4. (N(b) - N(a)) represents the number of events that occurred in the interval (a, b] with: 0 ≤ a < b.


# 3.2 Definition of a Poisson Process
A stochastic process {N(t), t ≥ 0} is said to be a Poisson process with intensity λ (λ > 0) if it satisfies the following conditions:

1. {N(t), t ≥ 0} is a counting process that satisfies the four previous conditions.

# 2- Process with Stationary Increments
The probability that k events occur in a time interval [t, t+h] depends only on the length of this interval h, so:
![](https://i.imgur.com/u40IInK.png)
This means that the process is homogeneous in time.


# 3.3 Process with Independent Increments
For any choice of reals 0 ≤ t₁ ≤ t₂ ... ≤ tₙ, the random variables: N(t₁), N(t₂)-N(t₁), N(t₃)-N(t₂), ... N(tₙ)-N(tₙ-₁) are independent. So the events that occur in disjoint time intervals are independent:
![](https://i.imgur.com/yHCXvJ0.png)
**Example:**

Let {N(t), t ≥ 0} be a Poisson process with intensity λ=5 events/hour, then:
![](https://i.imgur.com/nhKb1fY.png)
![](https://i.imgur.com/dBv2x2I.png)
![](https://i.imgur.com/HS2Obl4.png)
![](https://i.imgur.com/WLH8cFS.png)


3.4- The probability that two or more events occur in an infinitely small time interval (h → 0) is negligible compared to the probability that only one event occurs, so we can write¹:

![](https://i.imgur.com/uZpOcu3.png)
![](https://i.imgur.com/L3N9ryQ.png)
![](https://i.imgur.com/tnYCyDD.png)
![](https://i.imgur.com/hy0jU1m.png)
![](https://i.imgur.com/FTeOXyg.png)


# 3-2-1 Transition Graph
We can represent a Poisson process by the following transition matrix:
![](https://i.imgur.com/R8vtMq3.png)
So we can associate it with the graph given above:
![](https://i.imgur.com/xscSHPP.png)

# 3.3 Laws Associated with a Poisson Process
Law of the variable N(t)
![](https://i.imgur.com/geIt87G.png)
![](https://i.imgur.com/egf5wBA.png)
![](https://i.imgur.com/KAqznRc.png)
![](https://i.imgur.com/YmtR4NY.png)
![](https://i.imgur.com/yNB1AmY.png)
![](https://i.imgur.com/cnFaeOV.png)
![](https://i.imgur.com/flYCEEv.png)



# 3.4 Law of the Duration Between Two Consecutive Events
Let {N(t), t ≥ 0} be a Poisson process with intensity λ (λ > 0), and the duration separating the (n-1)th and the nth event.
**Theorem:**
![](https://i.imgur.com/8ZBfKD2.png)


The waiting times of a Poisson process with intensity λ are independent and identically distributed random variables according to an exponential law with parameter λ. (The time between two arrivals follows an exponential law)
![](https://i.imgur.com/Jd9KVoJ.png)
![](https://i.imgur.com/RNKDjS2.png)


![](https://i.imgur.com/HtBbesw.png)
![](https://i.imgur.com/n6xExIC.png)

# 3.4.1 Law of the Date When the nth Event Occurs
