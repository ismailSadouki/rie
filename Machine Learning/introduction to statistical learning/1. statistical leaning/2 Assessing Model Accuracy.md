# 1 Measuring the Quality of Fit
In the regression setting, the
most commonly-used measure is the mean squared error (MSE), given by
![](https://i.imgur.com/rSbUlsG.png)
We want to choose the method that gives
the lowest test MSE, as opposed to the lowest training MSE. In other words,
if we had a large number of test observations, we could compute
![](https://i.imgur.com/dPlcQrf.png)
the average squared prediction error for these test observations (x0 , y0 ).
![](https://i.imgur.com/eYvtQEP.png)

We’d like to select the model for which this quantity is as small as possible.
there is no guarantee that the method with the lowest training MSE will also
have the lowest test MSE.
Roughly speaking, the problem is that many
statistical methods specifically estimate coefficients so as to minimize the
training set MSE. For these methods, the training set MSE can be quite
small, but the test MSE is often much larger.
![](https://i.imgur.com/RmZVZov.png)
clear that as the level of flexibility increases, the curves fit the observed data more closely. The green curve is the most flexible and matches the
data very well; however, we observe that it fits the true f (shown in black)
poorly because it is too wiggly.By adjusting the level of flexibility of the
smoothing spline fit, we can produce many different fits to this data.
We now move on to the right-hand panel of Figure 2.9. The grey curve
displays the average training MSE as a function of flexibility, or more
formally the degrees of freedom, for a number of smoothing splines. <mark> The degrees of freedom is a quantity that summarizes the flexibility of a curve;</mark>
the test MSE initially declines as the level of flex-ibility increases.However, at some point the test MSE levels off and then starts to increase again.
The blue curve minimizes the test MSE, which should
not be surprising given that visually it appears to estimate f the best in the
left-hand panel of Figure 2.9. The horizontal dashed line indicates Var("),
the irreducible error. which corresponds to the lowest achievable
test MSE among all possible methods. Hence, the smoothing spline repre-
sented by the blue curve is close to optimal.

In the right-hand panel of Figure 2.9, as the flexibility of the statistical
learning method increases, we observe a monotone decrease in the training
MSE and a U-shape in the test MSE. This is a fundamental property of
statistical learning that holds regardless of the particular data set at hand
and regardless of the statistical method being used.As model flexibility
increases, the training MSE will decrease, but the test MSE may not.

When
a given method yields a small training MSE but a large test MSE, we are
said to be overfitting the data. This happens because our statistical learning
procedure is working too hard to find patterns in the training data, and
may be picking up some patterns that are just caused by random chance
rather than by true properties of the unknown function f .

Note that regardless of whether or not overfitting has
occurred, we almost always expect the training MSE to be smaller than
the test MSE because most statistical learning methods either directly or
indirectly seek to minimize the training MSE. <mark>Overfitting refers specifically to the case in which a less flexible model would have yielded a smaller test MSE.</mark>


![](https://i.imgur.com/hWAGGCL.png)
Figure 2.10 provides another example in which the true f is approxi-
mately linear. Again we observe that the training MSE decreases mono-
tonically as the model flexibility increases, and that there is a U-shape in
the test MSE. However, because the truth is close to linear, the test MSE
only decreases slightly before increasing again, so that the orange least
squares fit is substantially better than the highly flexible green curve.
![](https://i.imgur.com/Al7qvyJ.png)
Figure 2.11 displays an example in which f is highly non-linear. The
training and test MSE curves still exhibit the same general patterns, but
now there is a rapid decrease in both curves before the test MSE starts to
increase slowly.


# 2 The Bias-Variance Trade-Off
