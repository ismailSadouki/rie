Vector spaces are fundamental in many applications in NLP. If you were to represent a word, document, tweet, or any form of text, you will probably be encoding it as a vector. These vectors are important in tasks like information extraction, machine translation, and chatbots. Vector spaces could also be used to help you identify relationships between words as follows:
![](https://i.imgur.com/FjenyVT.png)
The famous quote by Firth says, **"You shall know a word by the company it keeps".** When learning these vectors, you usually make use of the neighboring words to extract meaning and information about the center word. If you were to cluster these vectors together, as you will see later in this specialization, you will see that adjectives, nouns, verbs, etc. tend to be near one another. Another cool fact, is that synonyms and antonyms are also very close to one another. This is because you can easily interchange them in a sentence and they tend to have similar neighboring words!

# Word by Word and Word by Doc.
**Word by Word Design**

We will start by exploring the word by word design. Assume that you are trying to come up with a vector that will represent a certain word. One possible design would be to create a matrix where each row and column corresponds to a word in your vocabulary. Then you can iterate over a document and see the number of times each word shows up next each other word. You can keep track of the number in the matrix. In the video I spoke about a parameter KKK. You can think of KKK as the bandwidth that decides whether two words are next to each other or not.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/Bl9oAms0R4yfaAJrNJeMMQ_d1093d07494248b6bcfd656363fba2d9_Screen-Shot-2021-02-11-at-11.14.20-AM.png?expiry=1756080000000&hmac=VRju1baYKQM-6h6UfjSYh5VIA3eWce2nmAlRXUdBT4E)

In the example above, you can see how we are keeping track of the number of times words occur together within a certain distance kkk. At the end, you can represent the word data, as a vector v=[2,1,1,0]v=[2,1,1,0]v, equals, open bracket, 2, comma, 1, comma, 1, comma, 0, close bracket.

**Word by Document Design**

You can now apply the same concept and map words to documents. The rows could correspond to words and the columns to documents. The numbers in the matrix correspond to the number of times each word showed up in the document.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/XXPXAWtOTAqz1wFrTqwK6w_741e4935c5ac435a96373dd745b124f9_Screen-Shot-2021-02-11-at-11.39.33-AM.png?expiry=1756080000000&hmac=cplM4aG2vJDv3RV4LSlkQNFMT_LPWoHZL7hSchQ_f4A)

You can represent the entertainment category, as a vector v=[500,7000]v=[500,7000]v, equals, open bracket, 500, comma, 7000, close bracket. You can then also compare categories as follows by doing a simple plot.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/IkrSkusORvCK0pLrDpbwRQ_3ab7a81281fb491dbd0736f83b095b2a_Screen-Shot-2021-02-11-at-11.49.47-AM.png?expiry=1756080000000&hmac=gBxgphQSzEHHEfBp4hectvgzWIfKGo4hHoAG4F9_CMU)

Later this week, you will see how you can use the angle between two vectors to measure similarity.
# Euclidean Distance
Let us assume that you want to compute the distance between two points: A,BA,BA, comma, B. To do so, you can use the euclidean distance defined as

![](https://i.imgur.com/XYSdBUH.png)


​d, left parenthesis, B, comma, A, right parenthesis, equals, square root of, left parenthesis, left parenthesis, B, start subscript, 1, end subscript, minus, A, start subscript, 1, end subscript, right parenthesis, squared, plus, left parenthesis, B, start subscript, 2, end subscript, minus, A, start subscript, 2, end subscript, right parenthesis, squared, right parenthesis, end square root

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/TCHxgBXJQoWh8YAVyXKFvA_e9f6c6b99e894229a31a59df4b059bb0_Screenshot-2021-03-08-at-6.22.29-PM.png?expiry=1756080000000&hmac=Ol1hCc4SphmLGhPNi6fFisVREq8xmqw8d2vcyxCak8A)

You can generalize finding the distance between the two points (A,B)(A,B)left parenthesis, A, comma, B, right parenthesis to the distance between an nnn dimensional vector as follows:

![](https://i.imgur.com/4wD0eqg.png)

​d, left parenthesis, v, with, vector, on top, comma, w, with, vector, on top, right parenthesis, equals, square root of, sum, start subscript, i, equals, 1, end subscript, start superscript, n, end superscript, left parenthesis, v, start subscript, i, end subscript, minus, w, start subscript, i, end subscript, right parenthesis, squared, end square root

Here is an example where I calculate the distance between 2 vectors (n=3n=3n, equals, 3).

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/ByYFxx0JTxSmBccdCd8UBg_632f3925e9ba4d8cbc63333ffd5333ce_Screen-Shot-2021-02-11-at-1.22.23-PM.png?expiry=1756080000000&hmac=TuL3wZJv9y31NH1reD90S_SG2xcp_bPELPKVhHAHqJ0)

# Cosine Similarity: Intuition
One of the issues with euclidean distance is that it is not always accurate and sometimes we are not looking for that type of similarity metric. For example, when comparing large documents to smaller ones with euclidean distance one could get an inaccurate result. Look at the diagram below:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/t58dUAq9TUOfHVAKvZ1DnQ_96a1d23debfc4594b4283993af9b5a18_Screen-Shot-2021-02-11-at-1.30.15-PM.png?expiry=1756080000000&hmac=f94sp01e_2YZ64cB8uxkP2RAjB1zEM2Eca8gtAzk33c)

Normally the **food** corpus and the **agriculture** corpus are more similar because they have the same proportion of words. However the food corpus is much smaller than the agriculture corpus. To further clarify, although the history corpus and the agriculture corpus are different, they have a smaller euclidean distance. Hence d2<d1d2​<d1​d, start subscript, 2, end subscript, is less than, d, start subscript, 1, end subscript.

To solve this problem, we look at the cosine between the vectors. This allows us to compare BBB and ααalpha.
# Cosine Similarity
![](https://i.imgur.com/vsjzoHP.png)

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/S8cJeaTtRjKHCXmk7cYydg_b1bb54b7f57b4640a35f7e55b23b9d2e_Screen-Shot-2021-02-11-at-2.00.20-PM.png?expiry=1756080000000&hmac=VKGhBCVbtVDc99WXnkktauuBhx7kNoD8arjbfLnaXGk)

The following cosine similarity equation makes sense:

![](https://i.imgur.com/OqhZ3lt.png)

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/XGzxCp9IS96s8QqfSAvelA_eb0ef7dab3554e23a0c84ebc37fd2aac_Screen-Shot-2021-02-11-at-2.13.53-PM.png?expiry=1756080000000&hmac=cRYlatlLo7ofw085soZo2-0ePQzO5ykEs6vuajjZRog)
# Manipulating Words in Vector Spaces
ou can use word vectors to actually extract patterns and identify certain structures in your text. For example:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/8TlSzBSNRpO5UswUjZaTpA_1b17a178c8784f58b6967ec192805a62_Screen-Shot-2021-02-11-at-2.17.43-PM.png?expiry=1756080000000&hmac=SxxLkA_YU1LT3q481iKyxwLwdeDF9l5vTBJftR3pQXo)

You can use the word vector for Russia, USA, and DC to actually compute a **vector** that would be very similar to that of Moscow. You can then use cosine similarity of the **vector** with all the other word vectors you have and you can see that the vector of Moscow is the closest. Isn't that cool?

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/wvTNxMPSRVO0zcTD0hVTYQ_c4290ce8360745679b1c70ee59c5d12e_Screen-Shot-2021-02-11-at-2.27.59-PM.png?expiry=1756080000000&hmac=sz0Q6WZekTo_Ju6dfXO-PUsRXOtixqcTF99a2jSFCGw)

Note that the distance (and direction) between a country and its capital is relatively the same. Hence manipulating word vectors allows you identify patterns in the text.
# Visualization and PCA
Principal component analysis is an unsupervised learning algorithm which can be used to reduce the dimension of your data. As a result, it allows you to visualize your data. It tries to combine variances across features. Here is a concrete example of PCA:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/XpCISFBMRjOQiEhQTJYz-A_2fc15dfc7025431c890259d8b0a4a9b7_Screen-Shot-2021-02-11-at-2.36.04-PM.png?expiry=1756080000000&hmac=aE06ZPPBfvYk-OGeW7cQ_gg095YmrlahuZX-Nasc7Eo)

Note that when doing PCA on this data, you will see that oil & gas are close to one another and town & city are also close to one another. To plot the data you can use PCA to go from d>2d>2d, is greater than, 2 dimensions to d=2d=2d, equals, 2.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/KCcN5Mx5TyenDeTMeY8ncA_4656f0cf7d3046178d4a112bd4ce8c79_Screen-Shot-2021-02-11-at-2.40.05-PM.png?expiry=1756080000000&hmac=kUVM5yH-u-3fyh2v5E1RP5NDX89G82G6kZYhyXnq2LA)

Those are the results of plotting a couple of vectors in two dimensions. Note that words with similar part of speech (POS) tags are next to one another. This is because many of the training algorithms learn words by identifying the neighboring words. Thus, words with similar POS tags tend to be found in similar locations. An interesting insight is that synonyms and antonyms tend to be found next to each other in the plot. Why is that the case?