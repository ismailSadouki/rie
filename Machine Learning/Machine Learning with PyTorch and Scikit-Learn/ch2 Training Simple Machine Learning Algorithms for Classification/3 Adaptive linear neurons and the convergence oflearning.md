In this section, we will take a look at another type of single-layer neural network (NN): ADAptive LInear NEuron (Adaline).
The Adaline algorithm is particularly interesting because it illustrates the key concepts of defining and minimizing continuous loss functions. 

The key difference between the Adaline rule (also known as the Widrow-Hoff rule) and Rosenblatt’s perceptron is that the weights are updated based on a linear activation function rather than a unit step function like in the perceptron. In Adaline this linear activation function, 𝜎(𝑧), is simply the identity function of the net input, so that $\sigma(z)=z$ .
While the linear activation function is used for learning the weights, we still use a threshold function to make the final prediction, which is similar to the unit step function that we covered earlier.
![](https://i.imgur.com/PcCyjtn.png)
As Figure 2.9 indicates, the Adaline algorithm compares the true class labels with the linear activation function’s continuous valued output to compute the model error and update the weights. In contrast, the perceptron compares the true class labels to the predicted class labels.

# Minimizing loss functions with gradient descent
In the case of Adaline, we can define the loss function, L, to learn the model
parameters as the mean squared error (MSE) between the calculated outcome and the true class label:
![](https://i.imgur.com/nTwuV3j.png)


![](https://i.imgur.com/7L6GMFe.png)

![](https://i.imgur.com/vBskRwk.png)



# Implementing Adaline in Python
Since the perceptron rule and Adaline are very similar, we will take the perceptron implementation that we defined earlier and change the fit method so that the weight and bias parameters are now updated by minimizing the loss function via gradient descent:
```
class AdalineGD:
    def __init__(self, eta=0.01, n_iter=50, random_state=1):
        self.eta = eta
        self.n_iter = n_iter
        self.random_state = random_state
    def fit(self, X, y):
        rgen = np.random.RandomState(self.random_state)
        self.w_ = rgen.normal(loc=0.0, scale=0.01, size=X.shape[1])
        self.b_ = np.float64(0.)
        self.losses_ = []

        for i in range(self.n_iter):
            net_input = self.net_input(X)
            output = self.activation(net_input)
            errors = (y-output)
            self.w_ += self.eta*2.0*X.T.dot(errors)/X.shape[0]
            self.b_ += self.eta*2.0*errors.mean()
            loss = (errors**2).mean()
            self.losses_.append(loss)
        return self

        
    def net_input(self, X):
        return np.dot(X, self.w_)+self.b_
    def activation(self, X):
        return X
    def predict(self, X):
        return np.where(self.activation(self.net_input(X) >= 0.5, 1, 0))
```
Instead of updating the weights after evaluating each individual training example, as in the perceptron, we calculate the gradient based on the whole training dataset.

However note that the weight updates via the partial derivatives $\frac{\sigma L}{\sigma w_J}$ 
involve the feature values xj, which we can compute by multiplying errors with each feature value for each weight:
```
for w_j in range(self.w_.shape[0]):
	self.w_[w_j] += self.eta *
		(2.0 * (X[:, w_j]*errors)).mean()
```
To implement the weight update more efficiently without using a for loop, we can use a matrix-vector multiplication between our feature matrix and the error vector instead:
```
self.w_ += self.eta * 2.0 * X.T.dot(errors) / X.shape[0]
```
Please note that the activation method has no effect on the code since it is simply an identity function. Here, we added the activation function (computed via the activation method) to illustrate the general concept with regard to how information flows through a single-layer NN: features from the input data, net input, activation, and output.


similar to the previous perceptron implementation, we collect the loss values in a self.losses_ list to check whether the algorithm converged after training.
![](https://i.imgur.com/LpRFBOb.png)
The left chart shows what could happen if we choose a learning rate that is too large. Instead of minimizing the loss function, the MSE becomes larger in every epoch, because we overshoot the global minimum. On the other hand, we can see that the loss decreases on the right plot, but the chosen learning rate,
𝜂=0.0001, is so small that the algorithm would require a very large number of epochs to converge to the global  loss minimum
![](https://i.imgur.com/UsVdyiJ.png)

# Improving gradient descent through feature scaling
Gradient descent is one of the many algorithms that benefit from feature scaling.
we will use a feature scaling method called standardization. This normalization procedure helps gradient descent learning to converge more quickly; however, it does not make the original dataset normally distributed.
to standardize the jth feature, we can simply subtract the sample mean, 𝜇𝑗, from every training example and divide it by its standard deviation, 𝜎𝑗:
![](https://i.imgur.com/rn9vXGu.png)
One of the reasons why standardization helps with gradient descent learning is that it is easier to find a learning rate that works well for all weights (and the bias).
If the features are on vastly different scales, a learning rate that works well for updating one weight might be too large or too small to update the other weight equally well.Overall, using standardized features can stabilize the training such that the optimizer has to go through fewer steps to find a good or optimal solution.
![](https://i.imgur.com/SPtraBc.png)
After standardization, we will train Adaline again and see that it now converges after a small number of epochs using a learning rate of 𝜂=0.5:
![](https://i.imgur.com/2NPeRlC.png)
note that the MSE remains non-zero even though all flower examples were classified correctly.

# Large-scale machine learning and stochastic gradient descent
we learned how to minimize a loss function by taking a step in the opposite
direction of the loss gradient that is calculated from the whole training dataset; this is why this approach is sometimes also referred to as <mark>full batch gradient descent</mark>.
Now imagine that we have a very large dataset with millions of data points, which is not uncommon in many machine learning applications. Running full batch gradient descent can be computationally quite costly in such scenarios.

A popular alternative to the batch gradient descent algorithm is <mark>stochastic gradient descent (SGD)</mark>, which is sometimes also called iterative or online gradient descent. Instead of updating the weights based on the sum of the accumulated errors over all training examples, $x^{(i)}$:
![](https://i.imgur.com/aEUHEfO.png)
we update the parameters incrementally for each training example, for instance:
![](https://i.imgur.com/mHdSUZA.png)
Although SGD can be considered as an approximation of gradient descent,it typically reaches convergence much faster because of the more frequent weight updates. Since each gradient is calculated based on a single training example, the error surface is noisier than in gradient descent, which can also have the advantage that SGD can escape shallow local minima more readily if we are working with nonlinear loss functions.
To obtain satisfying results via SGD, it is important to present training
data in a random order; also, we want to shuffle the training dataset for every epoch to prevent cycles.
![](https://i.imgur.com/DIDPLfD.png)
Another advantage of SGD is that we can use it for online learning. In online learning, our model is trained on the fly as new training data arrives. This is especially useful if we are accumulating large amounts of data.for example, customer data in web applications. Using online learning, the system
can immediately adapt to changes, and the training data can be discarded after updating the model if storage space is an issue.
![](https://i.imgur.com/aaSOyrR.png)
Since we already implemented the Adaline learning rule using gradient descent, we only need to make a few adjustments to modify the learning algorithm to update the weights via SGD.
Inside the fit method, we will now update the weights after each training example.
Furthermore, we will implement an additional partial_fit method, which does not reinitialize the weights, for online learning.
In order to check whether our algorithm converged after training, we will calculate the loss as the average loss of the training examples in each epoch. Furthermore, we will add an option to shuffle the training data before each epoch to avoid repetitive cycles when we are optimizing the loss function;;
via the random_state parameter, we allow the specification of a random seed for reproducibility:

راجع
very important raje3