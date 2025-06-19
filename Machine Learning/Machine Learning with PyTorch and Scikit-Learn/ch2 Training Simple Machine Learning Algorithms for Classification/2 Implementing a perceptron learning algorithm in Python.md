# OOP aproache
We will take an object-oriented approach to defining the perceptron interface as a Python class, which will allow us to initialize new Perceptron objects that can learn from data via a fit method and make predictions via a separate predict method. As a convention, we append an underscore (_) to attributes that are not created upon the initialization of the object, but we do this by calling the object’s other methods, for example, self.w_.


```
import numpy as np
class Perceptron:
    def __init__(self, eta=0.01, n_itr=50, random_state=1):
        self.eta = eta
        self.n_iter = n_iter
        self.random_state = random_state
    def fit(self, X, y):
        rgen = np.random.RandomState(self.random_state)
        self.w_ = rgen.normal(loc=0.0, scale=0.01, size=X.shape[1])
        self.b_ = np.float_(0.)
        self.errors_ = []

        for _ in range(self.n_iter):
            errors = 0
            for xi, target in zip(X, y):
                update=self.eta*(target-self.predict(xi))
                self.w_ +=update*xi
                self.b_+=update
                errors += int(update!=0.0)
            self.errors_.append(errors)
        return self
    def net_input(self, X):
        return np.dot(X, self.w_)+self.b_
    def predict(self, X):
        return np.where(self.net_input(x)>=0.0,1,0)
```
Using this perceptron implementation, we can now initialize new Perceptron objects with a given learning rate, eta (𝜂), and the number of epochs, n_iter (passes over the training dataset).
# Training a perceptron model on the Iris dataset
Note that we will also only consider two flower classes, setosa and versicolor, from the Iris dataset for practical reasons—remember, the perceptron is a binary classifier. However, the perceptron algorithm can be extended to multi-class classification—for example, <mark>the one-versus-all (OvA)</mark> technique.
![](https://i.imgur.com/YxeeBAa.png)


![](https://i.imgur.com/HpGlhTD.png)
Figure 2.6 shows the distribution of flower examples in the Iris dataset along the two feature axes: petallength and sepal length (measured in centimeters). In this two-dimensional feature subspace, we can see that a linear decision boundary should be sufficient to separate setosa from versicolor flowers. Thus, a linear classifier such as the perceptron should be able to classify the flowers in this dataset perfectly.

Now, it’s time to train our perceptron algorithm on the Iris data subset that we just extracted. Also,
we will plot the misclassification error for each epoch to check whether the algorithm converged and
found a decision boundary that separates the two Iris flower classes:
```
ppn = Perceptron(eta=0.1, n_iter=10)
ppn.fit(X,y)
plt.plot(range(1, len(ppn.errors_)+1), ppn.errors_, marker='o')
plt.xlabel('Epochs')
plt.ylabel('Number of updates')
```

Note that the number of misclassification errors and the number of updates is the same, since the perceptron weights and bias are updated each time it misclassifies an example.
![](https://i.imgur.com/1r0Ysxl.png)
As we can see in Figure 2.7, our perceptron converged after the sixth epoch and should now be able to classify the training examples perfectly.
![](https://i.imgur.com/r9yjWMI.png)
![](https://i.imgur.com/3YViS48.png)

