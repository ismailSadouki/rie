# Vocabulary & Feature Extraction
Given a tweet, or some text, you can represent it as a vector of dimension VVV, where VVV corresponds to your vocabulary size. If you had the tweet "I am happy because I am learning NLP", then you would put a 1 in the corresponding index for any word in the tweet, and a 0 otherwise.
![](https://i.imgur.com/rAHWOgA.png)
As you can see, as VVV gets larger, the vector becomes more sparse. Furthermore, we end up having many more features and end up training θθtheta VVV parameters. This could result in larger training time, and large prediction time.

# Feature Extraction with Frequencies
![](https://i.imgur.com/AFPDhus.png)

Given a corpus with positive and negative tweets as follows:![](https://i.imgur.com/jzLw7wU.png)
You have to encode each tweet as a vector. Previously, this vector was of dimension VVV. Now, as you will see in the upcoming videos, you will represent it with a vector of dimension 333. To do so, you have to create a dictionary to map the word, and the class it appeared in (positive or negative) to the number of times that word appeared in its corresponding class.
![](https://i.imgur.com/RzDnkSV.png)
we call this dictionary `freqs`. In the table above, you can see how words like happy and sad tend to take clear sides, while other words like "I, am" tend to be more neutral. Given this dictionary and the tweet, "I am sad, I am not learning NLP", you can create a vector corresponding to the feature as follows:
![](https://i.imgur.com/zbCdUIx.png)
To encode the negative feature, you can do the same thing.
![](https://i.imgur.com/4pGQodn.png)
Hence you end up getting the following feature vector [1,8,11][1,8,11]open bracket, 1, comma, 8, comma, 11, close bracket. 111 corresponds to the bias, 888 the positive feature, and 111111 the negative feature.
# Preprocessing
When preprocessing, you have to perform the following:

1. Eliminate handles and URLs
    
2. Tokenize the string into words.
    
3. Remove stop words like "and, is, a, on, etc."
    
4. Stemming- or convert every word to its stem. Like dancer, dancing, danced, becomes 'danc'. You can use porter stemmer to take care of this.
    
5. Convert all your words to lower case.

# Putting it all together

![](https://i.imgur.com/2ODJhD4.png)
Your X becomes of dimension (m,3)  as follows.
![](https://i.imgur.com/3uLGDg4.png)

![](https://i.imgur.com/i5mj1RP.png)
# Logistic Regression Overview
![](https://i.imgur.com/LzNVW9s.png)

To train your logistic regression function, you will do the following:
![](https://i.imgur.com/b47nFwT.png)
You initialize your parameter θθtheta, that you can use in your sigmoid, you then compute the gradient that you will use to update θθtheta, and then calculate the cost. You keep doing so until good enough.
# Logistic Regression: Testing
To test your model, you would run a subset of your data, known as the validation set, on your model to get predictions. The predictions are the outputs of the sigmoid function. If the output is ≥=0.5is greater than or equal to, equals, 0, point, 5, you would assign it to a positive class. Otherwise, you would assign it to a negative class.
![](https://i.imgur.com/6XcZptm.png)
![](https://i.imgur.com/Fmz62Zy.png)

# Naive Bayes Introduction
![](https://i.imgur.com/CSRdill.png)
This allows us compute the following table of probabilities:
![](https://i.imgur.com/KLXbk91.png)

Once you have the probabilities, you can compute the likelihood score as follows

![](https://i.imgur.com/u9xifkW.png)
A score greater than 1 indicates that the class is positive, otherwise it is negative.
# Laplacian Smoothing
![](https://i.imgur.com/10CnvVg.png)
# Log Likelihood, Part 1
To compute the log likelihood, we need to get the ratios and use them to compute a score that will allow us to decide whether a tweet is positive or negative. The higher the ratio, the more positive the word is:
![](https://i.imgur.com/1SsU7EA.png)
To do inference, you can compute the following:
![](https://i.imgur.com/R706G7U.png)
![](https://i.imgur.com/LoYCsBD.png)
Having the λλlambda dictionary will help a lot when doing inference.
# Log Likelihood Part 2
Once you computed the λλlambda dictionary, it becomes straightforward to do inference:![](https://i.imgur.com/c6ksZ9J.png)
# Training Naïve Bayes
To train your naïve Bayes classifier, you have to perform the following steps:

### 1) Get or annotate a dataset with positive and negative tweets

### 2) Preprocess the tweets: process_tweet(tweet) ➞ [w1, w2, w3, ...]:

- Lowercase
    
- Remove punctuation, urls, names
    
- Remove stop words
    
- Stemming
    
- Tokenize sentences
    

### 3) Compute freq(w, class):

![](https://i.imgur.com/62Xw3wu.png)
![](https://i.imgur.com/ilriCJT.png)
# Testing Naïve Bayes
