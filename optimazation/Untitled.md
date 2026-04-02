![](https://i.imgur.com/hLnfbZZ.png)
an optimization problem is solved only when a global
minimizer is found. However, global minimizers are, in general, difficult to
find. Therefore, in practice, we often have to be satisfied with finding local
minimizers.
![](https://i.imgur.com/4zMbmyo.png)
## 6.2 Conditions for Local Minimizers
![](https://i.imgur.com/ZuUSjd4.png)

a minimizer may lie either in the interior or on the boundary of Ω.
To study the case where it lies on the boundary, we need the notion of feasible directions
![](https://i.imgur.com/WFJp7i0.png)


![](https://i.imgur.com/MRP1drp.png)


![](https://i.imgur.com/awsUyT8.png)
![](https://i.imgur.com/xj2TXas.png)


![](https://i.imgur.com/kKIcuum.png)
![](https://i.imgur.com/2ALueVR.png)
![](https://i.imgur.com/mSRlDC9.png)

### Example 6.3
![](https://i.imgur.com/RK3ZwbM.png)
![](https://i.imgur.com/vquhWuU.png)
## Example 6.5
![](https://i.imgur.com/TfDrez7.png)


### Second-Order Necessary Condition (SONC).
![](https://i.imgur.com/s1nInxv.png)
### Interior Case.
![](https://i.imgur.com/HPgqRTf.png)
## Second-Order Sufficient Condition (SOSC), Interior Case.
![](https://i.imgur.com/GB8ZTdA.png)
### Example 6.8
![](https://i.imgur.com/ffNS38U.png)

---
---
# ONE-DIMENSIONAL SEARCH METHODS
## 7.2 Golden Section Search
## 7.3 Fibonacci Method
## 7.4 Bisection Method
## 7.5 Newton's Method
Suppose again that we are confronted with the problem of minimizing a func-
tion f of a single real variable x. We assume now that at each measurement
![](https://i.imgur.com/pUiPyDj.png)
![](https://i.imgur.com/4dY0MSQ.png)
Newton's method works well if f"(x) > 0 everywhere (see Figure 7.6).
However, if f"(x) < 0 for some x, Newton's method may fail to converge to
the minimizer (see Figure 7.7).
![](https://i.imgur.com/S4BfVzS.png)
## 7.6 Secant Method
Newton's method for minimizing f uses second derivatives of f :
![](https://i.imgur.com/NRxITtv.png)
If the second derivative is not available, we may attempt to approximate it
using first derivative information. In particular, we may approximate
second derivative of f at $x^{(k)}$ above with
![](https://i.imgur.com/S28LI8J.png)
Using the foregoing approximation of the second derivative, we obtain the
algorithm
![](https://i.imgur.com/owWjGLD.png)
called the secant method.


## 7.7 Bracketing

## 7.8 Line Search in Multidimensional Optimization
![](https://i.imgur.com/03oXHzr.png)
![](https://i.imgur.com/H2VoAKk.png)
Note that choice of $\alpha_k$ involves a one-dimensional minimization. This choice ensures that under appropriate conditions,
![](https://i.imgur.com/OjxOgfa.png)
![](https://i.imgur.com/Eu2jyuY.png)
The
basic idea is that we have to ensure that the step size ctk is not too small or
too large.
![](https://i.imgur.com/PQ5iwwv.png)
![](https://i.imgur.com/E4ghiWf.png)
![](https://i.imgur.com/D562MIx.png)
![](https://i.imgur.com/oiJFyIJ.png)


---
---
# 8. GRADIENT METHODS
![](https://i.imgur.com/GiK2mXp.png)

![](https://i.imgur.com/kUNjsrv.png)
![](https://i.imgur.com/sm7Dbcm.png)

![](https://i.imgur.com/zyebrOu.png)
We refer to this as a <mark>gradient descent algorithm</mark>
The gradient varies as the search proceeds, tending to zero as we
approach the minimizer.
We have the option of either taking very small steps
and reevaluating the gradient at every step, or we can take large steps each
time. The first approach results in a laborious method of reaching the mini-
mizer, whereas the second approach may result in a more zigzag path to the
minimizer.

## 8.2 The Method of Steepest Descent
![](https://i.imgur.com/1zb5xr6.png)
![](https://i.imgur.com/nB2XBQe.png)
![](https://i.imgur.com/Znq82WA.png)


![](https://i.imgur.com/Vu06TM3.png)
![](https://i.imgur.com/bC1oC4V.png)
![](https://i.imgur.com/bcjFNI2.png)
![](https://i.imgur.com/FXMmM13.png)
![](https://i.imgur.com/tFiZJgH.png)
![](https://i.imgur.com/owOXGjL.png)
The two (relative) stopping criteria above are preferable to the previous (abso-
lute) criteria because the relative criteria are "scale-independent."
![](https://i.imgur.com/QMRo9tn.png)
## 8.3 Analysis of Gradient Methods
#### The convergence analysis 
<mark>IMPORTANT BUT SKIPPED</mark>


# 9 NEWTON'S METHOD
Recall that the method of steepest descent uses only first derivatives (gra-
dients) in selecting a suitable search direction. This strategy is not always
the most effective. If higher derivatives are used, the resulting iterative al-
gorithm may perform better than the steepest descent method.
Newton's method uses first and second derivatives and indeed does perform better than the steepest descent method if the initial point is close to the minimizer.

The idea behind this method is as follows. Given a starting point, we construct a quadratic approximation to the objective function that matches the first and second derivative values at that point. We then minimize the approximate (quadratic) function instead of the original objective function. We use the minimizer of the approximate function as the starting point in the next step and repeat the procedure iteratively. If the objective function is quadratic, then the approximation is exact, and the method yields the true minimizer in one step.
If, on the other hand, the objective function is not quadratic, then the approximation will provide only an estimate of the position of the true minimizer.
![](https://i.imgur.com/G2xXlvx.png)
![](https://i.imgur.com/xFbGG2V.png)

### Example 9.1 Use Newton's method to minimize the Powell function:
![](https://i.imgur.com/aCB6qLp.png)
![](https://i.imgur.com/hRI5koA.png)
![](https://i.imgur.com/PnqXdZ2.png)

Observe that the kth iteration of Newton's method can be written in two
steps as
![](https://i.imgur.com/WrPlWwy.png)
![](https://i.imgur.com/IXz7CZd.png)


## 9.2 Analysis of Newton's Method
As in the one-variable case there is no guarantee that Newton's algorithm
heads in the direction of decreasing values of the objective function if
$F(x^{(k)})$ is not positive definite (recall Figure 7.7 illustrating Newton's method for
functions of one variable when f" < 0), Moreover, even if $F(x^{(k)})$ > 0 ,Newton's method may not be a descent method ,that is, it is possible that $f(x^{(k+1)}>= f(x^{(k)})$
For example, this may occur if our starting point x^0 is far away from the solution Despite these drawbacks, Newton's method has superior convergence properties when the starting point is near the solution,
![](https://i.imgur.com/sqCNK99.png)
![](https://i.imgur.com/9d8uPPc.png)
The convergence analysis of Newton's method when f is a quadratic func-
tion is straightforward. In fact, Newton's method reaches the point x* such  ![](https://i.imgur.com/2letrc4.png)
![](https://i.imgur.com/R3HNoqL.png)

![](https://i.imgur.com/fy8fIeu.png)
![](https://i.imgur.com/gPla8FF.png)
![](https://i.imgur.com/Qgf8pM7.png)

![](https://i.imgur.com/EXds8pW.png)
![](https://i.imgur.com/fmt0I3U.png)
Another source of potential problems in Newton's method arises from the
Hessian matrix not being positive definite. In the next section we describe a
simple modification of Newton's method to overcome this problem.

## 9.3 Levenberg-Marquardt Modification
![](https://i.imgur.com/EWOOnGW.png)
![](https://i.imgur.com/2mX9vHK.png)
![](https://i.imgur.com/7C8an7A.png)
## 9.4 Newton's Method for Nonlinear Least Squares
![](https://i.imgur.com/ESeUwtO.png)
![](https://i.imgur.com/x4jdS2Z.png)
![](https://i.imgur.com/ddHsOrW.png)
![](https://i.imgur.com/6kiob7D.png)
