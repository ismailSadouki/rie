Summing up what we have discussed so far, an objective function is a function inside your learning algorithm that measures how well the algorithm’s internal model is fitting the provided data. The objective function also provides feedback to the algorithm in order for it to improve its fit across successive iterations.
Clearly, since the entire algorithm’s efforts are recruited to perform well
based on the objective function, if the Kaggle evaluation metric perfectly matches the objective function of your algorithm, you will get the best results.

Unfortunately, this is not frequently the case. Often, the evaluation metric provided can only be approximated by existing objective functions. Getting a good approximation, or striving to get your predictions performing better with respect to the evaluation criteria, is the secret to performing well in Kaggle competitions. When your objective function does not match your evaluation metric, you have a few alternatives:

1. Modify your learning algorithm and have it incorporate an objective function that matches your evaluation metric, though this is not possible for all algorithms (for instance, algorithms such as LightGBM and XGBoost allow you to set custom objective functions, but most Scikit-learn models don’t allow this).
2. Tune your model’s hyperparameters, choosing the ones that make the result shine the most when using the evaluation metric.
3. Post-process your results so they match the evaluation criteria more closely. For instance, you could code an optimizer that performs transformations on your predictions (probability calibration algorithms are an example, and we will discuss them at the end of the chapter).
Having the competition metric incorporated into your machine learning algorithm is really the most effective method to achieve better predictions, though only a few algorithms can be hacked into using the competition metric as your objective function. The second approach is therefore the more common one, and many competitions end up in a struggle to get the best hyperparameters for your models to perform on the evaluation metric.

If you already have your evaluation function coded, then doing the right cross-validation or choosing the appropriate test set plays the lion share. If you don’t have the coded function at hand, you have to first code it in a suitable way, following the formulas provided by Kaggle.
doing the following will make the difference:
- Looking for all the relevant information about the evaluation metric and its coded function on a search engine
- Browsing through the most common packages
- Browsing GitHub projects
- In addition, as we mentioned before, querying the Meta Kaggle dataset (https://www.kaggle.com/kaggle/meta-kaggle) and looking in the Competitions table will help you find out which other Kaggle competitions used that same evaluation metric, and immediately provides you with useful code and ideas to try out

# Custom metrics and custom objective functions
As a first option when your objective function does not match your evaluation metric, we learned above that you can solve this by creating your own custom objective function, but that only a few algorithms can easily be modified to incorporate a specific objective function.

The good news is that the few algorithms that allow this are among the most effective ones in Kaggle competitions and data science projects. Of course, creating your own custom objective function may sound a little bit tricky, but it is an incredibly rewarding approach to increasing your score in a competition. For instance, there are options to do this when using gradient boosting algorithms such as XGBoost, CatBoost, and LightGBM, as well as with all deep learning models based on TensorFlow or PyTorch.

You can find great tutorials for custom metrics and objective functions in TensorFlow and PyTorch here:
- https://towardsdatascience.com/custom-metrics-in-keras-and-how-simple-they-are-to-use-in-tensorflow2-2-6d079c2ca279
- https://petamind.com/advanced-keras-custom-loss-functions/
- https://kevinmusgrave.github.io/pytorch-metric-learning/extend/losses/
```
If you want just to get straight to the custom objective function you need, you can try this Notebook by RNA (https://www.kaggle.com/bigironsphere): https://www.kaggle.com/bigironsphere/loss-function-library-keras-pytorch/notebook. It contains a large range of custom loss functions for both TensorFlow and PyTorch that have appeared in different competitions.
```
If you need to create a custom loss in LightGBM, XGBoost, or CatBoost, as indicated in their respective documentation, you have to code a function that takes as inputs the prediction and the ground truth, and that returns as outputs the gradient and the hessian.
```
You can consult this post on Stack Overflow for a better understanding of what a gradient and a hessian are: https://stats.stackexchange.com/questions/231220/how-to-compute-the-gradient-and-hessian-of-logarithmic-loss-question-is-based.
```

From a code implementation perspective, all you have to do is to create a function, using closures if you need to pass more parameters beyond just the vector of predicted labels and true labels. Here is a simple example of a focal loss (a loss that aims to heavily weight the minority class in the loss computations as described in Lin, T-Y. et al. Focal loss for dense object detection: https://arxiv.org/abs/1708.02002) function that you can use as a model for your own custom functions: 
![](https://i.imgur.com/5S89JWf.png)
![](https://i.imgur.com/IebBmXd.png)
In the above code snippet, we have defined a new cost function, focal_loss, which is then fed into an XGBoost instance’s object parameters. The example is worth showing because the focal loss requires the specification of some parameters in order to work properly on your problem (alpha and gamma). The more simplistic solution of having their values directly coded into the function is not ideal, since you may have to change them systematically as you are tuning your model. Instead, in the proposed function, when you input the parameters into the focal_loss function, they reside in memory and they are referenced by the loss_func function that is returned to XGBoost. The returned cost function, therefore, will work, referring to the alpha and gamma values that you have initially instantiated.

Another interestiAng aspect of the example is that it really makes it easy to compute the gradient and the hessian of the cost function by means of the derivative function from SciPy. If your cost function is differentiable, you don’t have to worry about doing any calculations by hand. How- ever, creating a custom objective function requires some mathematical knowledge and quite a lot of effort to make sure it works properly for your purposes. You can read about the difficulties that Max Halford experienced while implementing a focal loss for the LightGBM algorithm, and how he overcame them, here: https://maxhalford.github.io/blog/lightgbm-focal-loss/. Despite the difficulty, being able to conjure up a custom loss can really determine your success in a Kaggle competition where you have to extract the maximum possible result from your model.

If building your own objective function isn’t working out, you can simply lower your ambitions, give up building your function as an objective function used by the optimizer, and instead code it as a custom evaluation metric. Though your model won’t be directly optimized to perform against this function, you can still improve its predictive performance with hyperparameter optimization based on it. This is the second option we talked about in the previous section.

ust remember, if you are writing a metric from scratch, sometimes you may need to abide by certain code conventions for your function to work properly. For instance, if you use Scikit-learn, you have to convert your functions using the make_scorer function. The make_scorer function is actually a wrapper that makes your evaluation function suitable for working with the Scikit-learn API. It will wrap your function while considering some meta-information, such as whether to use probability estimates or predictions, whether you need to specify a threshold for prediction, and, last but not least, the directionality of the optimization, that is, whether you want to maximize or minimize the score it returns:
![](https://i.imgur.com/97WGVZ2.png)
In the above example, you prepare a scorer based on the average precision metric, specifying that it should use a weighted computation when dealing with multi-class classification problems.
![](https://i.imgur.com/e3mQFk1.png)
