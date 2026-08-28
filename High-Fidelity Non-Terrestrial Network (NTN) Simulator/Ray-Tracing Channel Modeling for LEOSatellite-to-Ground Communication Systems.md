

![](https://i.imgur.com/Xctpdwm.png)
# High-Fidelity NTN Simulator — Big Picture

Now that you've finished the paper, let's step back and connect it to the **actual project** rather than the paper's individual equations.

# The Big Picture

Your project is trying to build a **realistic simulator of satellite communications**.

At the moment, you're starting with the simplest important case:

$$
\boxed{\text{LEO satellite} \rightarrow \text{ground station, RF}}
$$

Eventually you'll extend this to FSO, inter-satellite links, networking, and AI.

---

# 1. What are we actually simulating?

Imagine a satellite transmitting:

$$
x(t)
$$

to a ground station.

The receiver doesn't get \(x(t)\) perfectly.

It gets something like:

$$
\boxed{
y(t)=\text{channel}(x(t))+\text{noise}
}
$$

The **channel** represents everything that happens to the signal while traveling from satellite to ground.

For your project, that includes:

- enormous propagation distance
- free-space path loss
- rain/atmospheric attenuation
- multipath from buildings
- Doppler caused by satellite movement
- potentially HPA distortion
- noise
- etc.

Your simulator's job is to model these effects.

---

# 2. Where does the satellite come from?

You don't manually specify:

> "At \(t=0\), satellite is here."

You start with real orbital information:

$$
\boxed{\text{TLE}}
$$

Then:

$$
\boxed{\text{TLE}\xrightarrow{\text{SGP4}}\mathbf r_s(t),\mathbf v_s(t)}
$$

So at every time \(t\), you know:

$$
\mathbf r_s(t)\in\mathbb R^3
$$

and

$$
\mathbf v_s(t)\in\mathbb R^3.
$$

That's your **moving satellite**.

---

# 3. Where is the ground station?

You define a ground receiver:

$$
\mathbf r_g\in\mathbb R^3.
$$

For example:

```text
                   🛰️
                 satellite
                    ↓
                    ↓
                    ↓
              🏙️ Manhattan
                    📡
               ground receiver
```

Now you know both ends of the communication link.

---

# 4. Determine the geometry

At every time \(t\), you calculate things like:

$$
D(t)=\text{satellite-ground distance}
$$

$$
\theta_{\mathrm{ele}}(t)=\text{elevation angle}
$$

$$
\theta_{\mathrm{az}}(t)=\text{azimuth angle}
$$

and relative velocity.

This tells you:

> **Where is the satellite relative to the receiver, and how is that geometry changing?**

---

# 5. This gives you Doppler

Because the satellite is moving:

$$
\mathbf v_s(t)\neq0
$$

you get a time-varying Doppler:

$$
\boxed{f_D(t)}
$$

Conceptually:

$$
\boxed{
\mathbf r_s(t),\mathbf v_s(t),\mathbf r_g
\rightarrow
f_D(t)
}
$$

This can then modify your transmitted signal.

---

# 6. This gives you large-scale path loss

The signal travels a huge distance.

So you calculate:

$$
PL_{fs}(t)
$$

and additional atmospheric/rain attenuation:

$$
PL_{rain}(t).
$$

Therefore:

$$
\boxed{
PL(t)=PL_{fs}(t)+PL_{rain}(t)
}
$$

This determines how much the signal power is reduced.

---

# 7. But distance isn't the whole story

This is where **ray tracing** enters.

Suppose the satellite signal reaches the city.

There isn't necessarily only one path:

```text
                    🛰️
                   / | \
                  /  |  \
                 /   |   \
                /    |    \
               ↓     ↓     ↘
             building       building
                \     ↓      /
                 \    ↓     /
                  \   ↓    /
                    📡
```

You can have:

- direct path
- reflected paths
- diffracted paths
- multiple paths with different delays.

Each path has approximately:

$$
(\alpha_l,\phi_l,\tau_l,f_{D,l})
$$

representing its:

- amplitude
- phase
- delay
- Doppler.

Together they form the channel.

---

# 8. This is where CIR comes from

The **Channel Impulse Response** is essentially a representation of those multipath components:

$$
\boxed{
h(t,\tau)
=
\sum_{l=1}^{L}
\alpha_l(t)e^{j\phi_l(t)}
\delta(\tau-\tau_l(t))
}
$$

You don't need to obsess over the equation.

The key idea is:

> **CIR tells you what happens to an impulse as it travels through the channel, including all the different paths.**

And **this is one of the main outputs your simulator ultimately needs.**

---

# 9. So your physical simulator becomes

At each time step:

$$
\boxed{
t
\rightarrow
\text{satellite position/velocity}
\rightarrow
\text{geometry}
\rightarrow
\text{channel}
}
$$

More concretely:

```text
TLE
 │
 ▼
SGP4
 │
 ├── position r(t)
 └── velocity v(t)
          │
          ▼
   Coordinate transformation
          │
          ▼
   Satellite-ground geometry
          │
    ┌─────┼──────────┐
    ▼     ▼          ▼
 distance elevation velocity
    │     │          │
    ▼     ▼          ▼
 path    rain      Doppler
 loss    loss
    │     │          │
    └──┬──┘          │
       │             │
       ▼             ▼
   large-scale    frequency
     effects       shift
       │             │
       └──────┬──────┘
              ▼
         Ray tracing
              │
              ▼
       Multipath paths
              │
              ▼
             CIR
              │
              ▼
       Received signal y(t)
```

---

# 10. And you repeat this over time

This is critical.

You don't create one channel.

You create a **time-varying channel**.

For:

$$
t_0,t_1,\ldots,t_N
$$

you get:

$$
h(t_0,\tau),
h(t_1,\tau),
\ldots,
h(t_N,\tau).
$$

So eventually you have something like:

$$
\boxed{
H\in\mathbb C^{N_t\times N_\tau}
}
$$

for a simple single-input/single-output representation.

Where:

- \(N_t\) = number of time steps
- \(N_\tau\) = number of delay samples
- \(H[n,m]\) = channel coefficient at time \(n\), delay \(m\).

That is much closer to the **tensor-based simulator** you want to build.

---

# 11. Then you add the actual communication system

The paper mostly focuses on **the channel**.

Your project goes further.

You eventually want:

$$
\boxed{
\text{bits}
\rightarrow
\text{modulation}
\rightarrow
\text{channel}
\rightarrow
\text{noise}
\rightarrow
\text{receiver}
\rightarrow
\text{bits}
}
$$

For example:

$$
\text{bits}
\rightarrow
\text{QAM}
\rightarrow
x
\rightarrow
h
\rightarrow
y
\rightarrow
\text{equalizer}
\rightarrow
\hat{x}.
$$

Then you can measure:

$$
BER
$$

$$
SNR
$$

$$
\text{outage probability}
$$

etc.

---

# 12. Then comes your AI part

This is where the project becomes especially relevant to **your background**.

You can create:

### Classical baseline

$$
\boxed{\text{MMSE equalizer}}
$$

and compare it with:

### AI model

$$
\boxed{\text{ResNet / RNN / Transformer}}
$$

For example:

$$
y[n]\in\mathbb C
$$

or equivalently I/Q:

$$
\mathbf y[n]
=
\begin{bmatrix}
I[n]\\Q[n]
\end{bmatrix}
\in\mathbb R^2.
$$

The neural network learns:

$$
\boxed{
\mathbf y
\rightarrow
\hat{\mathbf x}
}
$$

and you compare:

$$
BER_{\mathrm{MMSE}}
\quad\text{vs}\quad
BER_{\mathrm{AI}}.
$$

**This is where your ML knowledge becomes directly useful.**

---

# 13. Eventually your project becomes much bigger

The complete vision is approximately:

```text
                    NTN SIMULATOR
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
       PHY              NET              GUI
        │                │
   ┌────┴────┐       ┌───┴────┐
   │         │       │        │
   RF        FSO   Routing  Resource
   │         │       allocation
   │         │
   ▼         ▼
Channel    Optical
model      channel
   │
   ▼
CIR / CSI
   │
   ▼
Signal transmission
   │
   ▼
ML / classical algorithms
```

---

# 14. Where the paper fits

The paper you just finished is **not the whole project**.

It gives you a reference implementation/conceptual model for one slice:

$$
\boxed{
\text{LEO}
\rightarrow
\text{Ground}
\rightarrow
\text{RF channel}
}
$$

More specifically:

$$
\boxed{
\text{TLE}
\rightarrow
\text{SGP4}
\rightarrow
\text{trajectory}
\rightarrow
\text{geometry}
\rightarrow
\text{ray tracing}
\rightarrow
\text{path loss + multipath + Doppler}
}
$$

Your project then takes that idea and turns it into a **modular Python/PyTorch simulator**.

---

# The Most Important Mental Model

If you remember only one thing, remember this:

$$
\boxed{
\text{Where is the satellite?}
}
$$

↓

$$
\boxed{
\text{How does the environment affect the signal?}
}
$$

↓

$$
\boxed{
\text{What channel does the receiver experience?}
}
$$

↓

$$
\boxed{
\text{How well can we communicate through that channel?}
}
$$

↓

$$
\boxed{
\text{Can AI do better than classical methods?}
}
$$

That's your entire project.

And **you are currently at the second/third box**: learning how to turn a realistic LEO trajectory + physical environment into a realistic RF channel.

You do **not** need to become a telecommunications specialist before continuing. You need enough telecom knowledge to understand the interfaces and equations you're implementing, while your researcher provides the domain expertise.