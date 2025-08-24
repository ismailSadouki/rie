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

