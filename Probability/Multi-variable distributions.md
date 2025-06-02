The joint PMF determines the distribution because we can use it to find the prob-
ability of the event (X, Y ) ∈ A for any set A of points in the support of (X, Y ). All
we have to do is sum the joint PMF over A:

$$
P ((X, Y ) ∈ A) = sum((x,y)∈A ) 
P (X = x, Y = y).
$$

![](https://i.imgur.com/nBfzxZ9.png)

### Marginal PMF.
For discrete r.v.s X and Y , the marginal PMF
of X is
$$
P (X = x) =
sum(y) P (X = x, Y = y).
$$

The process of obtaining the marginal PMF from the joint PMF is illustrated in
Figure 7.2. Here we take a bird’s-eye view of the joint PMF for a clearer perspective;
each column of the joint PMF corresponds to a fixed x and each row corresponds
to a fixed y. For any x, the probability P (X = x) is the total height of the bars in
the corresponding column of the joint PMF: we can imagine taking all the bars in
that column and stacking them on top of each other to get the marginal probability.
Repeating this for all x, we arrive at the marginal PMF, depicted in bold.


![](https://i.imgur.com/MYxjJ8s.png)


### Marginal CDF.
Another way to go from joint to marginal distributions is via the joint CDF. In that
case, we take a limit rather than a sum: the marginal CDF of X is
$$
F X (x) = P (X ≤ x) = lim P (X ≤ x, Y ≤ y) = lim F X,Y (x, y)
$$
### Conditional PMF
For discrete r.v.s X and Y , the conditional PMF of Y given X = x is
$$
P (Y = y|X = x) =
P (X = x, Y = y)/P (X = x)
$$
This is viewed as a function of y for fixed x.

Figure 7.3 illustrates the definition of conditional PMF. To condition on the event
X = x, we first take the joint PMF and focus in on the vertical bars where X takes
on the value x; in the figure, these are shown in bold. All of the other vertical bars are
irrelevant because they are inconsistent with the knowledge that X = x occurred.
Since the total height of the bold bars is the marginal probability P (X = x),
we then re-normalize the conditional PMF by dividing by P (X = x); this ensures
that the conditional PMF will sum to 1. Therefore conditional PMFs are PMFs,
just as conditional probabilities are probabilities. Notice that there is a different
conditional PMF of Y for every possible value of X; Figure 7.3 highlights just one
of these conditional PMFs.

![[Screenshot from 2024-04-02 00-44-31.png]]


### Independence of discrete r.v.s.
Random variables X and Y are independent if for all x and y,
$$
F X,Y (x, y) = F X (x)F Y (y).
$$
If X and Y are discrete, this is equivalent to the condition
$$P (X = x, Y = y) = P (X = x)P (Y = y)$$
for all x, y, and it is also equivalent to the condition
$$P (Y = y|X = x) = P (Y = y)$$
for all x, y such that P (X = x) > 0.

the definition says that for independent  r.v.s, the joint CDF factors into the product of the marginal CDFs, or that the joint PMF factors into the product of the marginal PMFs.
Another way of looking at independence is that all the conditional PMFs are the same as the marginal PMF. That is, starting with the marginal PMF of Y , no updating is necessary when we condition on X = x, regardless of what x is.

## Continuous Case
### (Joint PDF).
If X and Y are continuous with joint CDF F X,Y , their joint PDF is the derivative of the joint CDF with respect to x and y:
$$
f X,Y (x, y) =
∂^2/∂x∂y[F X,Y (x, y)]
$$
We require valid joint PDFs to be nonnegative and integrate to 1:
$$f X,Y (x, y) ≥ 0$$and $$dobleIntgral(R)f X,Y (x, y)dxdy = 1 $$
Figure 7.4 shows a sketch of what a joint PDF of two r.v.s could look like. As
usual with continuous r.v.s, we need to keep in mind that the height of the surface
f X,Y (x, y) at a single point does not represent a probability. The probability of any
specific point in the plane is 0. Now that we’ve gone up a dimension, the proba-
bility of any line or curve in the plane is also 0. The only way we can get nonzero
probability is by integrating over a region of positive area in the xy-plane.
When we integrate the joint PDF over a region A, we are calculating the volume
under the surface of the joint PDF and above A. Thus, probability is represented by
volume under the joint PDF. The total volume under a valid joint PDF is 1.

![](https://i.imgur.com/tw2CP3T.png)


### (Marginal PDF).
For continuous r.v.s X and Y with joint PDF $fX,Y$ , the marginal PDF of X is
$$f X (x) =integral(+∞,-∞)
f X,Y (x, y)dy.$$
### (Conditional PDF).
For continuous r.v.s X and Y with joint PDF f X,Y , the conditional PDF of Y given X = x is
$$f Y |X (y|x) =
f X,Y (x, y)/
f X (x)$$
for all x with $f X (x) > 0$.
Figure 7.5 illustrates the definition of conditional PDF. We take a vertical slice of
the joint PDF corresponding to the observed value of X. Since the total area under
this slice is f X (x), we then divide by f X (x) to ensure that the conditional PDF will
have an area of 1. So the conditional PDF of Y given X = x satisfies the properties
of a valid PDF, for any x in the support of X.
![](https://i.imgur.com/m1GeSzD.png)


### (Continuous form of Bayes’ rule and LOTP)
X and Y , we have the following continuous form of Bayes’ rule:
$$
f Y |X (y|x) =
f X|Y (x|y)f Y (y)/f X (x) ,
for f X (x) > 0.
f X (x)
$$
And we have the following continuous form of the law of total probability:
$$
f X (x) = integral(R)
f X|Y (x|y)f Y (y)dy.
$$

### (Independence of continuous r.v.s).
Random variables X and Y are independent if for all x and y,
$$F X,Y (x, y) = F X (x)F Y (y).$$
If X and Y are continuous with joint PDF f X,Y , this is equivalent to the condition
$$f X,Y (x, y) = f X (x)f Y (y)$$
for all x, y, and it is also equivalent to the condition
$$f Y |X (y|x) = f Y (y)$$
for all x, y such that $f X (x) > 0.$

### (Covariance).
The covariance between r.v.s X and Y is
$$
Cov(X, Y ) = E((X − EX)(Y − EY )).
$$
Let’s think about the definition intuitively. If X and Y tend to move in the same
direction, then X − EX and Y − EY will tend to be either both positive or both
negative, so (X − EX)(Y − EY ) will be positive on average, giving a positive
covariance. If X and Y tend to move in opposite directions, then X − EX and
Y − EY will tend to have opposite signs, giving a negative covariance.
If X and Y are independent, then their covariance is zero. We say that r.v.s with
zero covariance are uncorrelated.
Covariance is a measure of linear association, so r.v.s can be dependent in nonlinear ways and still have zero covariance, as this example demonstrates. The bottom right plot of Figure 7.9 shows draws from the joint distribution of X and Y in this example. The other three plots illustrate positive correlation, negative correlation, and independence.
![](https://i.imgur.com/YtNtmEJ.png)

### (Correlation). 
The correlation between r.v.s X and Y is
$$
Corr(X, Y )=Cov(X, Y )/
Std(X)Std(Y )
$$
Correlation is convenient to interpret because it does not depend on the units of measurement and is always between −1 and 1.
$$
−1 ≤ Corr(X, Y ) ≤ 1.
$$
Covariance is a single-number summary of the tendency of two r.v.s to move in the
same direction. If two r.v.s are independent, then they are uncorrelated, but the
converse does not hold. Correlation is a unitless, standardized version of covariance
that is always between −1 and 1.
## Recap

![](https://i.imgur.com/YwhX1GH.png)

![](https://i.imgur.com/VfZyHhK.png)

![](https://i.imgur.com/a2VoXPU.png)
