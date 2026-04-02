More input features often make a predictive modeling task more challenging to model, more generally referred to as the curse of dimensionality.

High-dimensionality statistics and dimensionality reduction techniques are often used for data visualization. Nevertheless these techniques can be used in applied machine learning to simplify a classification or regression dataset in order to better fit a predictive model.

# 27.3 Dimensionality Reductio
it is often desirable to reduce the number of input features.
This reduces the number of dimensions of the feature space, hence the name dimensionality reduction.
```
When dealing with high dimensional data, it is often useful to reduce the dimensionality by projecting the data to a lower dimensional subspace which captures the “essence” of the data. This is called dimensionality reduction.
```

Fewer input dimensions often mean correspondingly fewer parameters or a simpler structure in the machine learning model, referred to as degrees of freedom. A model with too many degrees of freedom is likely to overfit the training dataset and therefore may not perform well on new data.
It is desirable to have simple models that generalize well, and in turn, input data with few input variables. This is particularly true for linear models where the number of inputs and the degrees of freedom of the model are often closely related.

```
The fundamental reason for the curse of dimensionality is that high-dimensional functions have the potential to be much more complicated than low-dimensional ones, and that those complications are harder to discern. The only way to beat the curse is to incorporate knowledge about the data that is correct.
```

Dimensionality reduction is a data preparation technique performed on data prior to modeling.It might be performed after data cleaning and data scaling and before training a predictive model.

As such, any dimensionality reduction performed on training data must also be performed on new data, such as a test dataset, validation dataset, and data when making a prediction with the final model.


# 27.4 Techniques for Dimensionality Reduction
### 27.4.1 Feature Selection Methods
```
... perform feature selection, to remove “irrelevant” features that do not help much with the classification problem.
```

### 27.4.2 Matrix Factorization
Techniques from linear algebra can be used for dimensionality reduction. Specifically, matrix factorization methods can be used to reduce a dataset matrix into its constituent parts. Examples include the eigendecomposition and singular value decomposition. The parts can then be ranked and a subset of those parts can be selected that best captures the salient structure of the matrix that can be used to represent the dataset. The most common method for ranking the components is principal components analysis, or PCA for short.

### 27.4.3 Manifold Learning
Techniques from high-dimensionality statistics can also be used for dimensionality reduction.
```
In mathematics, a projection is a kind of function or mapping that transforms data in some way.
```

These techniques are sometimes referred to as manifold learning and are used to create a low-dimensional projection of high-dimensional data, often for the purposes of data visualization. The projection is designed to both create a low-dimensional representation of the dataset whilst best preserving the salient structure or relationships in the data. Examples of manifold learning techniques include:
echniques include:
 Kohonen Self-Organizing Map (SOM).
 Sammons Mapping
 Multidimensional Scaling (MDS)
 t-distributed Stochastic Neighbor Embedding (t-SNE).
The features in the projection often have little relationship with the original columns, e.g. they do not have column names, which can be confusing to beginners.
### 27.4.4 Autoencoder Methods
Deep learning neural networks can be constructed to perform dimensionality reduction.
A popular approach is called autoencoders. This involves framing a self-supervised learning problem where a model must reproduce the input correctly. A network model is used that seeks to compress the data flow to a bottleneck layer with far fewer dimensions than the original input data. The part of the model prior to and including the bottleneck is referred to as the encoder, and the part of the model that reads the bottleneck output and reconstructs the input is called the decoder.
```
An auto-encoder is a kind of unsupervised neural network that is used for dimensionality reduction and feature discovery. More precisely, an auto-encoder is a feedforward neural network that is trained to predict the input itself.
```
After training, the decoder is discarded and the output from the bottleneck is used directly as the reduced dimensionality of the input. Inputs transformed by this encoder can then be fed into another model, not necessarily a neural network model.

```
Deep autoencoders are an effective framework for nonlinear dimensionality reduction. Once such a network has been built, the top-most layer of the encoder, the code layer hc, can be input to a supervised classification procedure.
```
The output of the encoder is a type of projection, and like other projection methods, there is no direct relationship to the bottleneck output back to the original input variables, making them challenging to interpret.

# 27.4.5 Tips for Dimensionality Reduction
There is no best technique for dimensionality reduction and no mapping of techniques to problems. Instead, the best approach is to use systematic controlled experiments to discover what dimensionality reduction techniques, when paired with your model of choice, result in the best performance on your dataset. <mark>Typically, linear algebra and manifold learning methods assume that all input features have the same scale or distribution. This suggests that it is good practice to either normalize or standardize data prior to using these methods if the input variables have differing scales or units. </mark>
