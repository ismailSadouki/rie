Trying to understand how the biological brain works in order to de-
sign an artificial intelligence (AI), Warren McCulloch and Walter Pitts published the first concept of a simplified brain cell, the so-called McCulloch-Pitts (MCP) neuron, in 1943 (A Logical Calculus of the Ideas Immanent in Nervous Activity by W. S. McCulloch and W. Pitts, Bulletin of Mathematical Biophysics, 5(4): 115-133, 1943).
Biological neurons are interconnected nerve cells in the brain that are involved in the processing and transmitting of chemical and electrical signals, which is illustrated in Figure 2.1:
![](https://i.imgur.com/dhrmGTf.png)
McCulloch and Pitts described such a nerve cell as a simple logic gate with binary outputs; multiple signals arrive at the dendrites, they are then integrated into the cell body, and, if the accumulated signal exceeds a certain threshold, an output signal is generated that will be passed on by the axon.

With his perceptron rule, Rosenblatt proposed an algorithm that would automatically learn the optimal weight coefficients that would then be multiplied with the input features in order to make the decision of whether a neuron fires (transmits a signal) or not. In the context of supervised learning and classification, such an algorithm could then be used to predict whether a new data point belongs to one class or the other.

# The formal definition of an artificial neuron
we can define a decision function, $𝜎(z)$, that takes a linear combination of certain input values, x, and a corresponding weight vector, w, where z is the so-called
net input $$z = w_1x_1 + w_2x_2 + ... + w_mx_m
$$
![](https://i.imgur.com/ga2syLx.png)
![](https://i.imgur.com/fDtOkMT.png)
let 
$$z>=\theta$$
$$z-\theta=0$$
Second, we define a so-called bias unit as $b=-\theta$ and make it part of the net input:
$$z=w^Tx+b$$
Third, given the introduction of the bias unit and the redefinition of the net input z above, we can redefine the decision function as follows:
![](https://i.imgur.com/goDY2Pu.png)

![](https://i.imgur.com/72kM343.png)

# The perceptron learning rule
The whole idea behind the MCP neuron and Rosenblatt’s thresholded perceptron model is to use a reductionist approach to mimic how a single neuron in the brain works: it either fires or it doesn’t. Thus, Rosenblatt’s classic perceptron rule is fairly simple, and the perceptron algorithm can be summarized by the following steps:
1.Initialize the weights and bias unit to 0 or small random numbers
2.For each training example, x^(i):
	a. Compute the output value, $\hat{𝑦}^{(i)}$ 
	b. Update the weights and bias unit
the simultaneous update of the bias unit and each weight, wj, in the weight vector, w, can be more formally written as:
![](https://i.imgur.com/Seadnxf.png)
The update values (“deltas”) are computed as follows:
![](https://i.imgur.com/HjHhnTs.png)
Note that unlike the bias unit, each weight, wj, corresponds to a feature, xj, in the dataset, which is involved in determining the update value,, Δ𝑤𝑗, defined above. Furthermore, 𝜂 is the learning rate


how beautifully simple this learning rule really is.
![](https://i.imgur.com/BlN4lgm.png)
It is important to note that the convergence of the perceptron is only guaranteed if the two classes are linearly separable.
![](https://i.imgur.com/cjcCSTn.png)
If the two classes can’t be separated by a linear decision boundary, we can set a maximum number of passes over the training dataset (epochs) and/or a threshold for the number of tolerated misclassifications—the perceptron would never stop updating the weights otherwise.


simple diagram that illustrates the general concept of the perceptron:
![](https://i.imgur.com/P3ww0NC.png)

