# Training and Visualizing a Decision Tree
You can visualize the trained Decision Tree by first using the export_graphviz()
method to output a graph definition file


![](https://i.imgur.com/dac4Tcp.png)

# Regularization Hyperparameters
Decision Trees make very few assumptions about the training data If left
unconstrained, the tree structure will adapt itself to the training data, fitting it very
closely, and most likely overfitting it. Such a model is often called a nonparametric
model, not because it does not have any parameters (it often has a lot) but because the
number of parameters is not determined prior to training, so the model structure is
free to stick closely to the data. In contrast, a parametric model such as a linear model
has a predetermined number of parameters, so its degree of freedom is limited,
reducing the risk of overfitting (but increasing the risk of underfitting)

To avoid overfitting the training data, you need to restrict the Decision Tree’s freedom
during training.this is called regularization. The regularization
hyperparameters depend on the algorithm used, but generally you can at least restrict
the maximum depth of the Decision Tree. In Scikit-Learn, this is controlled by the
max_depth hyperparameter. <mark>Reducing max_depth will regularize the model and thus reduce the risk of overfitting.</mark>

The DecisionTreeClassifier class has a few other parameters that similarly restrict
the shape of the Decision Tree: min_samples_split (the minimum number of sam‐
ples a node must have before it can be split), min_samples_leaf (the minimum num‐
ber of samples a leaf node must have), min_weight_fraction_leaf (same as
min_samples_leaf but expressed as a fraction of the total number of weighted
instances), max_leaf_nodes (maximum number of leaf nodes), and max_features
(maximum number of features that are evaluated for splitting at each node). Increas‐
ing min_* hyperparameters or reducing max_* hyperparameters will regularize the
model.

![](https://i.imgur.com/xgs8qen.png)


# Regression
Decision Trees are also capable of performing regression tasks.








# limitations
More generally, the main issue with Decision Trees is that they are very sensitive to
small variations in the training data.