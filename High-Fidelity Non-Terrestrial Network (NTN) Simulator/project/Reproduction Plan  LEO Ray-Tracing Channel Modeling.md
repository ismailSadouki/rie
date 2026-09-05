# Reproduction Plan — LEO Ray-Tracing Channel Modeling

Yes — this is a **very good project to build**, especially given what you're trying to learn from the two papers.

More importantly, I would keep the project structure you wrote. It has a clean dependency graph:

$$
\boxed{
\text{TLE}
\rightarrow
\text{SGP4}
\rightarrow
\text{Geometry}
\rightarrow
\text{Doppler}
\rightarrow
\text{Ray Tracing}
\rightarrow
\text{MPCs}
\rightarrow
\text{Channel Statistics}
\rightarrow
\text{3GPP Validation}
}
$$

The key thing is that you're **not really reproducing two papers independently**. You're building **one reusable LEO channel pipeline**, then using:

- **Paper 1** to validate the orbital/geometry/Doppler side.
- **Paper 2** to validate the multipath/statistical/channel-characterization side.
- **3GPP NTN** as the standardized reference.

---

## One Important Adjustment

Your milestone naming currently has:

> M2 = ray tracing  
> M3 = attenuation  
> M4 = fading  
> M5 = statistics

Conceptually, I'd think of it as **three layers**.

### Layer 1 — Geometry

$$
\boxed{\text{Geometry}}
$$

$$
\text{Satellite trajectory}
\rightarrow
(\mathbf r_s(t),\mathbf v_s(t))
\rightarrow
(\text{range},\text{azimuth},\text{elevation},\mathbf v_{\rm rel})
$$

---

### Layer 2 — Physical Channel

$$
\boxed{\text{Physical channel}}
$$

$$
\text{Scene}+\text{incidence}
\rightarrow
\left\{
\text{MPC}_i
\right\}_{i=1}^{N}
$$

where each MPC should ultimately look something like:

$$
\text{MPC}_i =
\left(
P_i,\,
\tau_i,\,
\phi_i^{\rm AoA},\,
\theta_i^{\rm AoA},\,
\phi_i^{\rm AoD},\,
\theta_i^{\rm AoD}
\right).
$$

---

### Layer 3 — Channel Characterization

$$
\boxed{\text{Channel characterization}}
$$

$$
\{\text{MPC}_i\}
\rightarrow
\begin{cases}
K\text{-factor}\\
\text{Rician/Loo parameters}\\
\sigma_\tau\\
\sigma_{\rm angular}\\
\text{DBSCAN clusters}
\end{cases}
$$

The attenuation models are somewhat **orthogonal** to the ray-tracing pipeline:

$$
L_{\rm total}
=
L_{\rm FSPL}
+
L_{\rm rain}
+
L_{\rm cloud}
+
L_{\rm snow}
+
L_{\rm misalignment}.
$$

That's worth keeping explicit in the implementation rather than mixing attenuation into the ray tracer.

---

# Your Real End Product

I wouldn't describe the final project simply as:

> "Reproduction of Ning et al. and Khawaja et al."

I'd describe it as:

> **Open-source reproduction and validation pipeline for LEO satellite-to-ground ray-tracing channels.**

And architect the repository around the physical pipeline:

```text
leo-channel-reproduction/
│
├── src/
│   ├── orbit/
│   │   ├── tle.py
│   │   ├── sgp4.py
│   │   └── geometry.py
│   │
│   ├── doppler/
│   │   ├── radial_velocity.py
│   │   └── doppler.py
│   │
│   ├── raytracing/
│   │   ├── scene.py
│   │   ├── incidence.py
│   │   └── mpc.py
│   │
│   ├── attenuation/
│   │   ├── friis.py
│   │   ├── rain.py
│   │   ├── cloud.py
│   │   ├── snow.py
│   │   └── misalignment.py
│   │
│   ├── fading/
│   │   ├── rician.py
│   │   ├── shadowed_rician.py
│   │   └── k_factor.py
│   │
│   ├── statistics/
│   │   ├── delay_spread.py
│   │   ├── angular_spread.py
│   │   └── clustering.py
│   │
│   └── validation/
│       ├── paper1.py
│       ├── paper2.py
│       └── threegpp.py
│
├── experiments/
│   ├── paper1_doppler/
│   ├── paper2_raytracing/
│   └── validation/
│
├── data/
│   ├── tle/
│   ├── scenes/
│   └── mpc/
│
├── tests/
│
├── notebooks/
│
└── README.md
```

This is important because **the code you write in M1 shouldn't die when you finish M1**.

For example, M1 produces:

$$
\mathbf r_s(t),\quad
\mathbf v_s(t),\quad
r(t),\quad
\Psi(t),\quad
\phi(t)
$$

and M2 consumes:

$$
\Psi(t),\phi(t).
$$

M2 produces:

$$
\{\tau_i,P_i,\Omega_i\}_{i=1}^{N}
$$

and M4/M5 consume those.

That's what makes this an actual engineering project rather than a collection of paper-reproduction notebooks.

---

# One Thing I Would NOT Do

Don't try to reproduce the papers numerically from day one.

For example, don't make your first objective:

$$
f_D^{\max} \stackrel{?}{=}43.7\text{ kHz}.
$$

Instead:

## Level 1 — Physics Correctness

Verify:

$$
f_D(t)
=
-\frac{v_r(t)}{c}f_c
$$

and:

$$
v_r(t)
=
\mathbf v_{\rm rel}(t)^\top
\hat{\mathbf u}_{\rm LOS}(t).
$$

Then verify:

$$
v_r(t_{\rm max\,elevation})\approx0
$$

so:

$$
f_D(t_{\rm max\,elevation})\approx0.
$$

---

## Level 2 — Paper-Scale Validation

Then ask:

$$
|f_D|_{\max}\sim 40\text{--}50\text{ kHz}
$$

at:

$$
f_c=2\text{ GHz}.
$$

---

## Level 3 — Paper Reproduction

Only then investigate why you're at, say:

$$
41.2\text{ kHz}
$$

instead of:

$$
43.7\text{ kHz}.
$$

That distinction is extremely important scientifically.

---

# M0 Should Be Tiny

Since you're currently at M0, don't spend hours building infrastructure.

Your M0 evidence contract can literally be:

> **A numerical result is valid only when its physical assumptions are explicitly stated.**

For every experiment, record:

```yaml
satellite:
  name:
  tle_epoch:

ground_station:
  latitude:
  longitude:
  altitude:

carrier:
  frequency_hz:

environment:
  scene:
  weather:

ray_tracer:
  engine:

assumptions:
```

Then every figure/table can be traced back to its conditions.

This will become **very valuable** when you start comparing against the papers.

---

# M1 Is Where I Would Spend Serious Effort

M1 looks simple:

$$
\text{TLE}\rightarrow\text{SGP4}\rightarrow\text{geometry}.
$$

But this is actually the foundation of everything.

Your core trajectory table should eventually be something like:

| $$t$$ | lat | lon | $$h$$ | range | elevation | azimuth | $$v_x$$ | $$v_y$$ | $$v_z$$ | $$v_r$$ | $$f_D$$ |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| $$t_0$$ | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |
| $$t_1$$ | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |
| ... | | | | | | | | | | | |

And mathematically, your geometry should be explicit:

$$
\mathbf r_{\rm rel}
=
\mathbf r_{\rm sat}-\mathbf r_{\rm gs}
$$

$$
R=\|\mathbf r_{\rm rel}\|_2
$$

$$
\hat{\mathbf u}_{LOS}
=
\frac{\mathbf r_{\rm rel}}
{\|\mathbf r_{\rm rel}\|_2}
$$

and:

$$
v_r=
\mathbf v_{\rm rel}^{T}
\hat{\mathbf u}_{LOS}.
$$

Then:

$$
f_D=-\frac{f_c}{c}v_r.
$$

That gives you a very clean chain of equations from **orbit → geometry → Doppler**.

---

# The Second Benefit for Your Larger Simulator

This is probably the most important reason I'd build it.

You're eventually trying to build the **NTN simulator**.

This project gives you reusable components:

```text
                    ┌──────────────┐
                    │     TLE      │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │    SGP4      │
                    └──────┬───────┘
                           ↓
                 ┌─────────────────────┐
                 │ Geometry / Kinematics│
                 └──────────┬──────────┘
                            │
              ┌─────────────┴──────────────┐
              ↓                            ↓
         Doppler                       Incidence
              │                            │
              │                            ↓
              │                       Ray tracing
              │                            │
              │                            ↓
              │                           MPCs
              │                            │
              └─────────────┬──────────────┘
                            ↓
                    Channel characterization
                            │
              ┌─────────────┼──────────────┐
              ↓             ↓              ↓
           fading       delay spread    angular spread
              │             │              │
              └─────────────┼──────────────┘
                            ↓
                       NTN simulator
```

So when you later implement your differentiable PyTorch/JAX simulator, you're **not starting from zero**.

You already know:

- what the coordinate transformations should produce,
- what Doppler should look like,
- what an MPC representation should contain,
- how atmospheric attenuation enters,
- how Rician/Loo fading is parameterized,
- what delay/angular spread mean,
- what a valid NTN sanity check looks like.

That's exactly why I agree with your **"feeds forward, not throwaway"** framing.

---

# Recommendation

**Follow this plan essentially as written. Don't expand the scope yet.**

Your progression should be:

$$
\boxed{
M0
\rightarrow
M1
\rightarrow
M2.1
\rightarrow
M2.2
\rightarrow
M2.3
\rightarrow
M3
\rightarrow
M4
\rightarrow
M5
\rightarrow
M6
}
$$

And I'd treat **M1 as the first real gate**:

> Don't move into Sionna RT until you can explain and validate every quantity coming out of the orbital/geometry pipeline.

Once M1 is correct, the rest becomes much easier conceptually:

**Paper 2 is no longer "another paper"; it's an extension of the same channel pipeline.**