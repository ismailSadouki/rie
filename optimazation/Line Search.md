
To rule out unacceptably short steps we introduce a second requirement,
called the curvature condition, which requires αk to satisfy
![](https://i.imgur.com/fsVut1E.png)
for some constant c2 ∈ (c1 , 1), Note that the left-hand-
side is simply the derivative $φ (α_k )$, so the curvature condition ensures that the slope of $φ(α_k )$ is greater than $c_2$ times the gradient $φ (0)$. This makes sense because if the slope φ (α) is strongly negative, we have an indication that we can reduce f signiﬁcantly by moving further along the chosen direction. On the other hand, if the slope is only slightly negative or even positive, it is a sign that we cannot expect much more decrease in f in this direction,
![](https://i.imgur.com/xdfGnjP.png)
![](https://i.imgur.com/5YopFTf.png)
![](https://i.imgur.com/XPTQJEW.png)
with 0 < c1 < c2 < 1. The only difference with the Wolfe conditions is that we no longer allow the derivative φ (αk ) to be too positive. Hence, we exclude points that are far from stationary points of φ.

![](https://i.imgur.com/6i8MvRn.png)
## THE GOLDSTEIN CONDITIONS
the Goldstein conditions also ensure that the step length
α achieves sufﬁcient decrease while preventing α from being too small.
![](https://i.imgur.com/ezlALSJ.png)
![](https://i.imgur.com/hVQoaPM.png)

A disadvantage ﬁrst inequality in (3.11) may exclude all minimizers of φ
Goldstein conditions are often used in Newton-type methods but are not well suited for quasi-Newton methods that maintain a positive deﬁnite Hessian approximation.

## SUFFICIENT DECREASE AND BACKTRACKING
by using a so-called backtracking approach, we can dispense with the extra condition (3.6b) and use just the sufﬁcient decrease condition to terminate the line search procedure. In its most basic form, backtracking proceeds as follows.
![](https://i.imgur.com/gToRciq.png)
![](https://i.imgur.com/cs4IktK.png)
In this procedure, the initial step length ᾱ is chosen to be 1 in Newton and quasi-Newton methods, but can have different values in other algorithms such as steepest descent or conjugate gradient.


# 3.2 CONVERGENCE OF LINE SEARCH METHODS
![](https://i.imgur.com/cip28Q6.png)
![](https://i.imgur.com/hBugr9e.png)
Inequality (3.14), which we call the Zoutendijk condition, implies that
![](https://i.imgur.com/uYQyZ4c.png)
This limit can be used in turn to derive global convergence results for line search algorithms.
If our method for choosing the search direction pk in the iteration (3.1) ensures that the angle θk deﬁned by (3.12) is bounded away from 90◦ , there is a positive constant δ such that
![](https://i.imgur.com/uzqfgJI.png)
In other words, we can be sure that the gradient norms ∇fk converge to zero, provided that the search directions are never too close to orthogonality with the gradient.
In particular, the method of steepest descent (for which the search direction pk makes an angle of zero degrees with the negative gradient) produces a gradient sequence that converges to zero, provided that it uses a line search satisfying the Wolfe or Goldstein conditions.
![](https://i.imgur.com/SddHLaJ.png)
, and if the step lengths satisfy the Wolfe conditions.


# 3.3 RATE OF CONVERGENCE
It would seem that designing optimization algorithms with good convergence properties is easy, since all we need to ensure is that the search direction pk does not tend to become orthogonal to the gradient ∇fk , or that steepest descent steps are taken regularly.
We could simply compute cos θk at every iteration and turn pk toward the steepest descent direction if cos θk is smaller than some preselected constant δ > 0.Angle tests of this type ensure global convergence, but they are undesirable for two reasons. First, they may impede a fast rate of convergence, because for problems with an ill-conditioned Hessian, it may be necessary to produce search directions that are almost orthogonal to the gradient, and an inappropriate choice of the parameter δ may prevent this. Second, angle tests destroy the invariance properties of quasi-Newton methods.

The challenge is to design algorithms that incorporate both properties:
good global convergence guarantees and a rapid rate of convergence.

## CONVERGENCE RATE OF STEEPEST DESCENT
![](https://i.imgur.com/1KPyRFD.png)
Since ∇fk = Qxk − b, this equation yields a closed-form expression for xk+1 in terms of xk .




nonlinear programming berteskas