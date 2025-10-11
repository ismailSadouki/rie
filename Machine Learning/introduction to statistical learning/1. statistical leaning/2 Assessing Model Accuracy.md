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
The U-shape observed in the test MSE curves (Figures 2.9–2.11) turns out
to be the result of two competing properties of statistical learning methods.
![](https://i.imgur.com/xjYBOwm.png)
e need to select a statistical learning method that simultaneously achieves
low variance and low bias. Note that variance is inherently a nonnegative
quantity, and squared bias is also nonnegative. Hence, we see that the
expected test MSE can never lie below Var("), the irreducible error from
(2.3).
Variance refers to the amount by which fˆ would change if we
estimated it using a different training data set.
if a method has high variance then small changes in the training data can result in large changes in fˆ. In general, more flexible statistical methods have higher variance.
Consider the green and orange curves in Figure 2.9. The flexible green curve is following the observations very closely. It has high variance because changing any one of these data points may cause the estimate fˆ to change considerably.
In contrast, the orange least squares line is relatively inflexible and has low
variance, because moving any single observation will likely cause only a
small shift in the position of the line.

bias refers to the error that is introduced by approxi-
mating a real-life problem, which may be extremely complicated, by a much
simpler model.
For example, linear regression assumes that there is a linear
relationship between Y and X1 , X2 , . . . , Xp . It is unlikely that any real-life
problem truly has such a simple linear relationship, and so performing lin-
ear regression will undoubtedly result in some bias in the estimate of f.
![](https://i.imgur.com/BtwP7Wh.png)
In
Figure 2.11, the true f is substantially non-linear, so no matter how many
training observations we are given, it will not be possible to produce an
accurate estimate using linear regression. In other words, linear regression
results in high bias in this example. However, in Figure 2.10 the true f
is very close to linear, and so given enough data, it should be possible for
linear regression to produce an accurate estimate. Generally, more flexible
methods result in less bias.

<mark>As a general rule, as we use more flexible methods, the variance will
increase and the bias will decrease.</mark>
The relative rate of change of these
two quantities determines whether the test MSE increases or decreases. As
we increase the flexibility of a class of methods, the bias tends to initially
decrease faster than the variance increases. Consequently, the expected
test MSE declines. However, at some point increasing flexibility has little
impact on the bias but starts to significantly increase the variance. When
this happens the test MSE increases. Note that we observed this pattern
of decreasing test MSE followed by increasing test MSE in the right-hand
panels of Figures 2.9–2.11.
In the left-hand panel of Figure 2.12, the
bias initially decreases rapidly, resulting in an initial sharp decrease in the
expected test MSE. On the other hand, in the center panel of Figure 2.12
the true f is close to linear, so there is only a small decrease in bias as flex-
ibility increases, and the test MSE only declines slightly before increasing
rapidly as the variance increases. Finally, in the right-hand panel of Fig-
ure 2.12, as flexibility increases, there is a dramatic decline in bias because
the true f is very non-linear. There is also very little increase in variance
as flexibility increases. Consequently, the test MSE declines substantially
before experiencing a small increase as model flexibility increases.

Good test set performance of a statistical learning method requires low variance as well as low squared bias. This is referred to as a trade-off because it is easy to obtain a method with extremely low bias but high variance (for instance, by drawing a curve that passes through every
single training observation) or a method with very low variance but high
bias (by fitting a horizontal line to the data). The challenge lies in finding
a method for which both the variance and the squared bias are low.

o take an extreme example, suppose that the true f is linear.
In this situation linear regression will have no bias, making it very hard
for a more flexible method to compete. In contrast, if the true f is highly
non-linear and we have an ample number of training observations, then
we may do better using a highly flexible approach.

## 3 The Classification Setting
The
most common approach for quantifying the accuracy of our estimate fˆ is
the training error rate, the proportion of mistakes that are made if we applyour estimate fˆ to the training observations:
![](https://i.imgur.com/WLB8KcT.png)
![](https://i.imgur.com/kCKJoY6.png)

#### The Bayes Classifier
It is possible to show (though the proof is outside of the scope of this
book) that the test error rate given in (2.9) is minimized, on average, by a
very simple classifier that assigns each observation to the most likely class,
given its predictor values. In other words, we should simply assign a test
observation with predictor vector x0 to the class j for which
![](https://i.imgur.com/HY1u4N4.png)
is largest.

This very simple clas-sifier is called the Bayes classifier. In a two-class problem where there areonly two possible response values, say class 1 or class 2, the Bayes classifier corresponds to predicting class one if Pr(Y = 1|X = x0 ) > 0.5, and class two otherwise.
![](https://i.imgur.com/3pjHqnN.png)
The orange shaded region reflects the set of points for which Pr(Y = orange|X) is greater than 50 %
, while the blue
shaded region indicates the set of points for which the probability is below
50 %.The purple dashed line represents the points where the probability
is exactly 50 %. This is called the Bayes decision boundary.![](https://i.imgur.com/qHVIHWR.png)
### K-Nearest Neighbor
In theory we would always like to predict qualitative responses using the
Bayes classifier. But for real data, we do not know the conditional distri-
bution of Y given X, and so computing the Bayes classifier is impossi-
ble. Therefore, the Bayes classifier serves as an unattainable gold standard
against which to compare other methods. Many approaches attempt to
estimate the conditional distribution of Y given X, and then classify a
given observation to the class with highest estimated probability.
One such
method is the K-nearest neighbors (KNN) classifier. Given a positive in-teger K and a test observation x0 ,the KNN classifier first identifies the
K points in the training data that are closest to x0 , represented by N0 .
It then estimates the conditional probability for class j as the fraction of
points in N0 whose response values equal j:![](https://i.imgur.com/d0Doo3N.png)
Finally, KNN classifies the test observation x0 to the class with the largest
probability from (2.12).
![](https://i.imgur.com/eaI4NWF.png)
Despite the fact that it is a very simple approach, KNN can often pro-
duce classifiers that are surprisingly close to the optimal Bayes classifier.![](https://i.imgur.com/QOuLCVU.png)
Figure 2.15 displays the KNN decision boundary, using K = 10, when ap-
plied to the larger simulated data set from Figure 2.13. Notice that even
though the true distribution is not known by the KNN classifier, the KNN
decision boundary is very close to that of the Bayes classifier. The test error
rate using KNN is 0.1363, which is close to the Bayes error rate of 0.1304.![](https://i.imgur.com/tDVttjs.png)
Figure 2.16 displays two KNN fits to the simulated data from Figure 2.13,
using K = 1 and K = 100. When K = 1, the decision boundary is overly
flexible and finds patterns in the data that don’t correspond to the Bayes
decision boundary. This corresponds to a classifier that has low bias but
very high variance. As K grows, the method becomes less flexible and
produces a decision boundary that is close to linear. This corresponds to
a low-variance but high-bias classifier. On this simulated data set, neither
K = 1 nor K = 100 give good predictions: they have test error rates of
0.1695 and 0.1925, respectively.
![](https://i.imgur.com/8WifARp.png)
In Figure 2.17, we have
plotted the KNN test and training errors as a function of 1/K. As 1/K in-
creases, the method becomes more flexible. As in the regression setting, the
training error rate consistently declines as the flexibility increases.
However,
the test error exhibits a characteristic U-shape, declining at first (with a
minimum at approximately K = 10) before increasing again when the
method becomes excessively flexible and overfits.

In both the regression and classification settings, choosing the correct
level of flexibility is critical to the success of any statistical learning method.
The bias-variance tradeoff, and the resulting U-shape in the test error, can
make this a difficult task.