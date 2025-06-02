we say $θ^∗$ is a local minimum if
![](https://i.imgur.com/IXN2Rhc.png)
A local minimum could be surrounded by other local minima with the same objective value; this is known as a **flat local minimum**. A point is said to be a strict local minimum if its cost is strictly lower than those of neighboring points:
![](https://i.imgur.com/qBdbT8y.png)
if an algorithm is guaranteed to converge to a stationary point
from any starting point, it is called globally convergent. However, this does not mean (rather confusingly) that it will converge to a global optimum; instead, it just means it will converge to some stationary point.

# 8.1.1.1 Optimality conditions for local vs global optima

For continuous, twice differentiable functions Let g(θ) = ∇L(θ) and $H(θ) = ∇^2 L(θ)$
Consider a point $θ^∗ ∈ R^D$ , and let $g^ ∗ = g(θ)|_{θ^∗}$ and $H^∗ = H(θ)|_{θ^∗}$
![](https://i.imgur.com/yTU2hXu.png)


# 8.1.2 Constrained vs unconstrained optimization
We define the feasible set as the subset of the parameter space that satisfies the constraints:
![](https://i.imgur.com/BqUTni7.png)
Our constrained optimization problem now becomes
![](https://i.imgur.com/x9pmxoI.png)
If $C = R^D$ , it is called unconstrained optimization.
The addition of constraints can change the number of optima of a function

A common strategy for solving constrained problems is to create penalty terms that measure how much we violate each constraint. We then add these terms to the objective and solve an unconstrained optimization problem. The Lagrangian is a special case of such a combined objective


# 8.1.3 Convex vs nonconvex optimization
In convex optimization, we require the objective to be a convex function defined over a convex set. In such problems, every local minimum is also a global minimum.Thus many models are designed so that their training objectives are convex.
## 8.1.3.1 Convex sets
We say S is a convex set if, for any $x, x` ∈ S$, we have
![](https://i.imgur.com/mTnc9Um.png)
That is, if we draw a line from x to x' , all points on the line lie inside the set.
## 8.1.3.2 Convex functions
We say f is a convex function if its epigraph defines a convex set.
![](https://i.imgur.com/rFctWVx.png)
Equivalently, a function f (x) is called convex if it is defined on a
convex set and if, for any x, y ∈ S, and for any 0 ≤ λ ≤ 1, we have
![](https://i.imgur.com/xfn9vWr.png)
![](https://i.imgur.com/CaKNhUh.png)
A function is called strictly convex if the inequality is strict. A function f (x) is concave if −f (x) is convex, and strictly concave if −f (x) inequality is strictly convex.
## 8.1.3.3 Characterization of convex functions
![](https://i.imgur.com/QICrDPE.png)

![](https://i.imgur.com/3QRxEd9.png)

## 8.1.3.4 Strongly convex functions
We say a function f is strongly convex with parameter m > 0 if the following holds for all x, y in f ’s domain:
![](https://i.imgur.com/M9mMnIy.png)
A strongly convex function is also strictly convex, but not vice versa.![](https://i.imgur.com/1gcL3ZX.png)
![](https://i.imgur.com/8flL273.png)
![](https://i.imgur.com/qcEpgrQ.png)

# 8.1.4 Smooth vs nonsmooth optimization
In smooth optimization, the objective and constraints are continuously differentiable functions.
For smooth functions, we can quantify the degree of smoothness using the Lipschitz constant. In the 1d case, this is defined as any constant L ≥ 0 such that, for all real x1 and x2 , we have
![](https://i.imgur.com/GLERMPE.png)
![](https://i.imgur.com/bNV0XdZ.png)
This is illustrated in Figure 8.8: for a given constant L, the function output cannot change by more than L if we change the function input by 1 unit. This can be generalized to vector inputs using a suitable norm
In nonsmooth optimization, there are at least some points where the gradient of the objective function or the constraints is not well-defined.In some optimization problems, we can partition the objective into a part that only contains smooth terms, and a part that contains the nonsmooth terms:
![](https://i.imgur.com/ook0ign.png)

# 8.1.4.1 Subgradients
![](https://i.imgur.com/pdHFyKf.png)
Note that a subgradient can exist even when f is not differentiable at a point
![](https://i.imgur.com/SBEitAU.png)
![](https://i.imgur.com/DsR0TfY.png)
![](https://i.imgur.com/EDHzInq.png)
