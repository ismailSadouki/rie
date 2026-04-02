# Overview

Word embeddings are used in most NLP applications. Whenever you are dealing with text, you first have to find a way to encode the words as numbers. Word embedding are a very common technique that allows you to do so. Here are a few applications of word embeddings that you should be able to implement by the time you complete the specialization.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/zFY5jJV4QZqWOYyVeNGaQQ_e77be068ecee4e40b3cb1cd402090f2b_Screen-Shot-2021-03-26-at-2.10.26-PM.png?expiry=1756166400000&hmac=JA7bfbMb_Czdypt9Etbyc30GPEGbggxaEM7vSi1Znos)

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/OlFz0AS-SQqRc9AEvkkKuA_0c78d63e962a483d83428fa0014d153a_Screen-Shot-2021-03-26-at-2.10.19-PM.png?expiry=1756166400000&hmac=OkiS_Ezv8BBCTUhf_M1JNjVm4Ivs8dluNvg15inFRXU)

By the end of this week you will be able to:

- Identify the key concepts of word representations
    

- Generate word embeddings
    

- Prepare text for machine learning
    

- Implement the continuous bag-of-words model
# Basic Word Representations

Basic word representations could be classified into the following:

- Integers
    

- One-hot vectors
    

- Word embeddings
    

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/p1ISbqzJTtWSEm6syS7VlQ_47c983d709ef48e193082828b054ce46_Screen-Shot-2021-03-26-at-2.15.25-PM.png?expiry=1756166400000&hmac=juyKxA0QKfljE50f8eBIuDexpfWL4F8o6iWRot-IGd4)

To the left, you have an example where you use integers to represent a word. The issue there is that there is no reason why one word corresponds to a bigger number than another. To fix this problem we introduce one hot vectors (diagram on the right). To implement one hot vectors, you have to initialize a vector of **zeros** of dimension _V_ and then put a 1 in the index corresponding to the word you are representing.

The **pros** of one-hot vectors: simple and require no implied ordering.

The **cons** of one-hot vectors: huge and encode no meaning.
# Word Embeddings

So why use word embeddings? Let's take a look.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/hD4WFHuBS46-FhR7gfuOag_8b91201a0eda46f4bbac8e7a1b5b9f1b_Screen-Shot-2021-03-26-at-2.30.22-PM.png?expiry=1756166400000&hmac=UrvLSojpPpqlmp5mSMERGAzYxTLyRmfGoc-kSvwTxLc)

From the plot above, you can see that when encoding a word in 2D, similar words tend to be found next to each other. Perhaps the first coordinate represents whether a word is positive or negative. The second coordinate tell you whether the word is abstract or concrete. This is just an example, in the real world you will find embeddings with hundreds of dimensions. You can think of each coordinate as a number telling you something about the word.

The **pros**:

- Low dimensions (less than V)
    
- Allow you to encode meaning



# How to Create Word Embeddings?

To create word embeddings you always need a corpus of text, and an embedding method.

The context of a word tells you what type of words tend to occur near that specific word. The context is important as this is what will give meaning to each word embedding.

## Embeddings

There are many types of possible methods that allow you to _learn_ the word embeddings. The machine learning model performs a learning task, and the main by-products of this task are the word embeddings. The task could be to learn to predict a word based on the surrounding words in a sentence of the corpus, as in the case of the continuous bag-of-words.

The task is **self-supervised**: it is both unsupervised in the sense that the input data — the corpus — is unlabelled, and supervised in the sense that the data itself provides the necessary context which would ordinarily make up the labels. 

When training word vectors, there are some parameters you need to tune. (i.e. the dimension of the word vector)

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/_kHLokKKR4eBy6JCimeHEQ_8a4517594b234bc9972c816f5d90644f_Screen-Shot-2021-03-26-at-4.56.35-PM.png?expiry=1756166400000&hmac=PNk4K-SJMi7NJjktYSY2QyTZX7U5zBBrze_6B2AeJog)

# Word Embedding Methods

## Classical Methods

- word2vec (Google, 2013)
    
- _Continuous bag-of-words (CBOW)__:_ the model learns to predict the center word given some context words.
    
- _Continuous skip-gram / Skip-gram with negative sampling (SGNS)_: the model learns to predict the words surrounding a given input word.
    

- _Global Vectors (GloVe) (Stanford, 2014)_: factorizes the logarithm of the corpus's word co-occurrence matrix, similar to the count matrix you’ve used before.
    

- _fastText (Facebook, 2016)__:_ based on the skip-gram model and takes into account the structure of words by representing words as an n-gram of characters. It supports out-of-vocabulary (OOV) words.
    

## Deep learning, contextual embeddings

 In these more advanced models, words have different embeddings depending on their context. You can download pre-trained embeddings for the following models.

- BERT (Google, 2018):
    

- ELMo (Allen Institute for AI, 2018)
    

- GPT-2 (OpenAI, 2018)

# Continuous Bag of Words Model

To create word embeddings, you need a corpus and a learning algorithm. The by-product of this task would be a set of word embeddings. In the case of the continuous bag-of-words model, the objective of the task is to predict a missing word based on the surrounding words.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/o744WuCZQO6-OFrgmfDuJA_91f4eb4536a347a089e17d654d296947_Screen-Shot-2021-03-29-at-9.24.15-AM.png?expiry=1756166400000&hmac=f7mBGG6CVkIYE3pX5NRtlIKEl6XFQPRx1e8E_kAuOPU)

Here is a visualization that shows you how the models works.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/_84SgGXiSNSOEoBl4rjUxw_537e5eb6ce7e49cdac4f3adccb702fde_Screen-Shot-2021-03-29-at-9.26.34-AM.png?expiry=1756166400000&hmac=cN1AKsIe04voEocguvEB6FxnIDJJybB6BfzL8qv1yeE)

As you can see, the window size in the image above is 5. The context size, C, is 2. C usually tells you how many words before or after the center word the model will use to make the prediction. Here is another visualization that shows an overview of the model.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/IgFIDDSeRTWBSAw0nmU1fg_a466dc9d154649b1a5c1f85cabc7799a_Screen-Shot-2021-03-29-at-10.28.27-AM.png?expiry=1756166400000&hmac=7e_fI4ursn0fjqwTdtTSwbM2rB89pIviV1_7n8hLs_Y)

# Cleaning and Tokenization

Before implementing any natural language processing algorithm, you might want to clean the data and tokenize it. Here are a few things to keep track of when handling your data.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/ur0mznE0QzO9Js5xNNMz-Q_7c33998c0cd94306b1d3bfe38e9d9e22_Screen-Shot-2021-03-29-at-10.33.16-AM.png?expiry=1756166400000&hmac=r6-7rK00TfkiJ65g2vP67YfWMBeZYpzNjSXzayKOwmo)

You can clean data using python as follows:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/xEZ1XH0_RQ-GdVx9P9UP8w_a1fafb1bfca94bb283ec91fb96623465_Screen-Shot-2021-03-29-at-10.36.20-AM.png?expiry=1756166400000&hmac=zQBah-10kOrSQ3Fe7yo4l1GPsNFJa9VrpvLfTz5TSo0)

You can add as many conditions as you want in the lines corresponding to the green rectangle above.


# Sliding Window of words in Python

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/5GCbimNSSeKgm4pjUlni1g_33285a93db5647628c0e62b82ed879ac_Screen-Shot-2021-03-29-at-10.41.52-AM.png?expiry=1756166400000&hmac=zJwkRSXuRh01nfQFKpJD4OrFXtAQWe1IQWPwjd5KVHU)

The code above shows you a function which takes in two parameters.

- Words: a list of words.
    
- C: the context size.
    

We first start by setting _i_ to C. Then we single out the center_word, and the context_words. We then yield those and increment _i_.

# Transforming Words into Vectors

To transform the context vectors into one single vector, you can use the following.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/47PW2XCtQeKz1tlwrTHilw_7f6ae18977bd49608fc6f76490893e88_Screen-Shot-2021-03-29-at-10.47.19-AM.png?expiry=1756166400000&hmac=ofFIwLTiRh7bstYGa5dEyB1PDC68WniAHQ12BMgfp3Y)

As you can see, we started with one-hot vectors for the context words and and we transform them into a single vector by taking an average. As a result you end up having the following vectors that you can use for your training.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/_8tnmwRxT9mLZ5sEca_ZoQ_da23a564385f4daa8832aafd8ce7b048_Screen-Shot-2021-03-29-at-10.50.14-AM.png?expiry=1756166400000&hmac=COcmPFpJTqbKVPidI4ONqdhmNed5bcGOAIUZoMum0DE)
# Architecture for the CBOW Model

The architecture for the CBOW model could be described as follows

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/MV2Hwa94QTydh8GveDE83A_1a282cab7d6d457c945e792b9b7d4d04_Screen-Shot-2021-03-29-at-10.52.02-AM.png?expiry=1756166400000&hmac=bUSK52hSvkcqX74G32kaXhBWJsi9P-GsCHFFbof641I)

You have an input, _X,_ which is the average of all context vectors. You then multiply it by W1W1​W, start subscript, 1, end subscript and add b1b1​b, start subscript, 1, end subscript. The result goes through a ReLU function to give you your hidden layer. That layer is then multiplied by W2W2​W, start subscript, 2, end subscript and you add b2b2​b, start subscript, 2, end subscript. The result goes through a softmax which gives you a distribution over _V,_ vocabulary words. You pick the vocabulary word that corresponds to the arg-max of the output.

# Architecture of the CBOW Model: Dimensions

When dealing with batch input, you can stack the examples as columns. You can then proceed to multiply the matrices as follows:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/_DOUQiywR4uzlEIssMeLgA_9b4153e307a04ec1aae1a48fe375b58b_Screen-Shot-2021-03-29-at-1.48.05-PM.png?expiry=1756166400000&hmac=wSs4MeZbPuIZUuu3eazo9a7xsdpOWGdY4pqOag-BMYw)

In the diagram above, you can see the dimensions of each matrix. Note that your Y^Y^Y, with, hat, on top is of dimension V by m. Each column is the prediction of the column corresponding to the context words. So the first column in Y^Y^Y, with, hat, on top is the prediction corresponding to the first column of _X_.

# Training a CBOW Model: Cost Function

The cost function for the CBOW model is a cross-entropy loss defined as:
![](https://i.imgur.com/zlBgN6s.png)
Here is an example where you use the equation above.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/3e9XY2U5QESvV2NlOVBEaA_ecfe29fb3a854989b46134538b770cad_Screen-Shot-2021-03-29-at-2.18.32-PM.png?expiry=1756166400000&hmac=Gsu6EfGPWCBaho9ngmt_jjl_TlAlMrqfU3Oy8LXYCAA)

Why is the cost 4.61 in the example above?

# Training a CBOW Model: Forward Propagation

Forward propagation is defined as:

![](https://i.imgur.com/PrchYbf.png)


In the image below you start from the left and you forward propagate all the way to the right.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/HQHbUH6SRGSB21B-khRkAw_4de75f2577b54b48b0b3c23f4949ae1c_Screen-Shot-2021-03-29-at-2.31.33-PM.png?expiry=1756166400000&hmac=96sazL0bk3nUXLeADtJ0EkLvqDD102qBocD_xYteJJo)

To calculate the loss of a batch, you have to compute the following:
![](https://i.imgur.com/Of0uglO.png)
Given, your predicted center word matrix, and actual center word matrix, you can compute the loss.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/jWwcSjGdRXasHEoxnbV2zA_549e684c70274a4586b6f060c8dc192d_Screen-Shot-2021-03-29-at-2.38.51-PM.png?expiry=1756166400000&hmac=2AxiEuOmbszZr9_ZBPNOJM6IW4Mx43ALh_V5Nc7265g)

# Training a CBOW Model: Backpropagation and Gradient Descent

- **Backpropagation**: calculate partial derivatives of cost with respect to weights and biases.
    

When computing the back-prop in this model, you need to compute the following:
![](https://i.imgur.com/y8Zkupo.png)
A smaller alpha allows for more gradual updates to the weights and biases, whereas a larger number allows for a faster update of the weights. If ααalpha is too large, you might not learn anything, if it is too small, your model will take forever to train.
# Extracting Word Embedding Vectors

There are two options to extract word embeddings after training the continuous bag of words model. You can use w1w1​w, start subscript, 1, end subscript as follows:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/azjmJmHaTN245iZh2hzdrw_361c5259933343a088e3ff29c6ca2aaa_Screen-Shot-2021-03-29-at-5.12.19-PM.png?expiry=1756166400000&hmac=-iJtrw4HY4joMTlHl8EsQc7ek3BXUod4rvZc4MzHH9U)

If you were to use w1w1​w, start subscript, 1, end subscript, each column will correspond to the embeddings of a specific word. You can also use w2w2​w, start subscript, 2, end subscript as follows:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/zAjCoCbFS5iIwqAmxauYAg_cd348843b8a74f37aafb58ee8619f5cc_Screen-Shot-2021-03-29-at-5.15.22-PM.png?expiry=1756166400000&hmac=kJXvID_MYeCB3fBU6WtYqUwYp4LF32wZAb52S23TQks)

The final option is to take an average of both matrices as follows:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/pdvDAJzeS6ebwwCc3tunfA_42ee7a09c22a4a1f9e0fb081591ac948_Screen-Shot-2021-03-29-at-5.16.12-PM.png?expiry=1756166400000&hmac=x_h3L8QzHZeemoC8QEheXMd1aJ3lLUuUlDuSy9PZIVQ)

# Evaluating Word Embeddings: Intrinsic Evaluation

**Intrinsic evaluation** allows you to test relationships between words. It allows you to capture semantic analogies as, _“France” is to “Paris” as “Italy” is to <?>_ and also syntactic analogies as _“seen” is to “saw” as “been” is to <?>__._

_Ambiguous_ cases could be much harder to track:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/WYeHySclSXKHh8knJQlyOg_1b311a595e654d6f8c03dfc66dbc1cbd_Screen-Shot-2021-03-29-at-5.26.53-PM.png?expiry=1756166400000&hmac=jrUS5sg5-T3KPTebdyxcd0_Ri7p6INYACAYuHzocu-I)

Here are a few ways that allow to use intrinsic evaluation.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/vQBs53_BSlqAbOd_wapaWg_fad8253825684cfdb89980713be06f9e_Screen-Shot-2021-03-29-at-5.28.00-PM.png?expiry=1756166400000&hmac=CjQC_7LGLhE1MNfPCQjzA-0etfRwpOtDeb5VDqdLw_0)

# Evaluating Word Embeddings: Extrinsic Evaluation

**Extrinsic evaluation** tests word embeddings on external tasks like named entity recognition, parts-of-speech tagging, etc.

- + Evaluates actual usefulness of embeddings
    
- - Time Consuming
    
- - More difficult to trouble shoot
    

So now you know both **intrinsic** and **extrinsic** evaluation.