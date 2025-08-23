# Transforming word vectors
In the previous week, I showed you how we can plot word vectors. Now, you will see how you can take a word vector and learn a mapping that will allow you to translate words by learning a "transformation matrix". Here is a visualization:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/6_gRc_NRSza4EXPzUWs2IQ_4b3c0a4193444b2eb20dd73544755559_Screen-Shot-2021-02-22-at-9.01.23-AM.png?expiry=1756080000000&hmac=oGPClGvOhVKfVDBUGKT5jrmhvOdCsnpwcF1wQwzZlrQ)

Note that the word "chat" in french means cat. You can learn that by taking the vector corresponding to "cat" in english, multiplying it by a matrix that you learn and then you can use cosine similarity between the output and all the french vectors. You should see that the closest result is the vector which corresponds to "chat".

Here is a visualization of that showing you the aligned vectors:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/NWrTGJbMQzqq0xiWzDM6NA_9ed7e91d9206497e80cf1c4db61ae086_Screen-Shot-2021-02-22-at-9.05.57-AM.png?expiry=1756080000000&hmac=t0UPBNsEY6Ygt_gyJYPRMXio92EyQ7qyDsqpIQBsAvM)

Note that XXX corresponds to the matrix of english word vectors and YYY corresponds to the matrix of french word vectors. RRR is the mapping matrix.
![](https://i.imgur.com/C4YYLVe.png)
![](https://i.imgur.com/7ATAvMm.png)

# K-nearest neighbors

After you have computed the output of XR you get a vector. You then need to find the most similar vectors to your output. Here is a visual example:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/XaXETsw4RLulxE7MOFS7kg_b2bd6570f342403fb53dfe39f491dc0b_Screen-Shot-2021-02-22-at-9.54.21-AM.png?expiry=1756080000000&hmac=03xp-NXin54yNsJwRYjDsKj18ytbtKC26pLC2Swr5GQ)

In the video, we mentioned if you were in San Francisco, and you had friends all over the world, you would want to find the nearest neighbors. To do that it might be expensive to go over all the countries one at a time. So we will introduce hashing to show you how you can do a look up much faster.

# Hash tables and hash functions
Imagine you had to cluster the following figures into different buckets:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/DJ8LrbRZRaCfC620WeWgsA_dd8f9ee92cf04db5854e75f223156615_Screen-Shot-2021-02-22-at-10.42.40-AM.png?expiry=1756080000000&hmac=dwSdWlW5QgKjI_oqWSwtf1o4CzHbZjKLYQ3j0LDuCcg)

Note that the figures blue, red, and gray ones would each be clustered with each other

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/ZuLLQhi0Spiiy0IYtLqY7Q_66cf201e756d4656994e5f9cf75d9c76_Screen-Shot-2021-02-22-at-10.45.17-AM.png?expiry=1756080000000&hmac=kfiYkExptJTKKUz195WYWuDL3x0cgEQJx8CQpDSfLIo)

You can think of hash function as a function that takes data of arbitrary sizes and maps it to a fixed value. The values returned are known as _hash values_ or even _hashes_.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/-jUz98wpQBG1M_fMKUARoQ_a53d277a33b842bbad8461520b586fe5_Screen-Shot-2021-02-22-at-10.54.47-AM.png?expiry=1756080000000&hmac=l3IF4uZh1mWNfei-PoYHqnFGoNm8ERvIH0LJP5yEHPA)

The diagram above shows a concrete example of a hash function which takes a vector and returns a value. Then you can mod that value by the number of buckets and put that number in its corresponding bucket. For example, 14 is in the 4th bucket, 17 & 97 are in the 7th bucket. Let's take a look at how you can do it using some code.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/wmu-l05gRu6rvpdOYPbuhA_1215d919fc9e4cf3813ee1e9f14c2e9e_Screenshot-2021-03-08-at-6.46.41-PM.png?expiry=1756080000000&hmac=ukt6SX8uhigCTol0us-BQWbhzkZfeT-yTEtVB8ni0bY)

The code snippet above creates a basic hash table which consists of hashed values inside their buckets. **hash_function** takes in _value_l_ (a list of values to be hashed) and _n_buckets_ and mods the value by the buckets. Now to create the _hash_table,_ you first initialize a list to be of dimension _n_buckets (_each value will go to a bucket)_._ For each value in your list of values, you will feed it into your **hash_function,** get the _hash_value,_ and append it to the list of values in the corresponding bucket.

Now given an input, you don't have to compare it to all the other examples, you can just compare it to all the values in the same _hash_bucket_ that input has been hashed to.

When hashing you sometimes want similar words or similar numbers to be hashed to the same bucket. To do this, you will use “locality sensitive hashing.”  Locality is another word for “location”.  So locality sensitive hashing is a hashing method that cares very deeply about assigning items based on where they’re located in vector space.

# Locality sensitive hashing
Locality sensitive hashing is a technique that allows you to hash similar inputs into the same buckets with high probability.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/Os9-kXG0Q7GPfpFxtPOxOg_238c5102ee1e4d2a855a8b6b788b7ef5_Screen-Shot-2021-02-22-at-12.08.24-PM.png?expiry=1756080000000&hmac=Nw8ZdUjV0cL3pSrt_t_Q7N8XzFmrYIGmwNrWhYqznH0)

Instead of the typical buckets we have been using, you can think of clustering the points by deciding whether they are above or below the line. Now as we go to higher dimensions (say n-dimensional vectors), you would be using planes instead of lines. Let's look at a concrete example:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/iIFTDSVzRaaBUw0lcwWmRg_c18ba446a5c84362999974cbb339819d_Screen-Shot-2021-02-22-at-12.13.30-PM.png?expiry=1756080000000&hmac=XLOn9b6kkaKnNTfyBBbRtBXXWRTiF7Rq4VMZ3VUBsdw)

![](https://i.imgur.com/PxWB4Il.png)


Here is how to visualize a projection (i.e. a dot product between two vectors):

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/LSFSDm5bQvyhUg5uW4L8Ig_d5929a276ec547bd9a597b71a08e2962_Screen-Shot-2021-02-22-at-12.37.45-PM.png?expiry=1756080000000&hmac=7h94a2szfdYHnrn8C5hdth7_JGqC8zRtiJU8v2XT6N8)

When you take the dot product of a vector V1V1​V, start subscript, 1, end subscript and a PPP, then you take the magnitude or length of that vector, you get the black line (labelled as Projection). The sign indicates on which side of the plane the projection vector lies.

# Multiple Planes
You can use multiple planes to get a single hash value. Let's take a look at the following example:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/7nskw35IR9-7JMN-SDffHA_fd71b19d60544bae99fdb3ab112d6267_Screen-Shot-2021-02-22-at-1.14.33-PM.png?expiry=1756080000000&hmac=9xOcC0wbAza-ViDhvR4V8MCl3wl6RWM5UepmsMjct8U)
![](https://i.imgur.com/8epqiR7.png)
![](https://i.imgur.com/GNFAB8q.png)
# Approximate nearest neighbors
Approximate nearest neighbors does not give you the full nearest neighbors but gives you an approximation of the nearest neighbors. It usually trades off accuracy for efficiency. Look at the following plot:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/phiXuy1WQHaYl7stVmB2Jg_9033e2a9f3d0496887a9e7953e34105a_Screen-Shot-2021-02-22-at-1.35.41-PM.png?expiry=1756080000000&hmac=GpTIl3Cdgz8FEkggdcWDS8WRxAwuGr4EvvqCkR_M56g)

You are trying to find the nearest neighbor for the red vector (point). The first time, the plane gave you green points. You then ran it a second time, but this time you got the blue points. The third time you got the orange points to be the neighbors. So you can see as you do it more times, you are likely to get all the neighbors. Here is the code for one set of random planes. Make sure you understand what is going on.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/AJrRL4UjR3Wa0S-FI9d1pA_7c9838fa2cac4b39bf623443337f2a2e_Screen-Shot-2021-02-22-at-1.40.40-PM.png?expiry=1756080000000&hmac=LXm71CISehTskJh7NyFww5utDzUvIV_jnbYZHgMevkE)

# Searching documents
The previous video shows you a toy example of how you can actually represent a document as a vector.

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/620nGgp6TaetJxoKel2nwQ_40de3e1790574e8d920d1601422cf869_Screen-Shot-2021-02-22-at-1.43.43-PM.png?expiry=1756080000000&hmac=on__pLmBNnCm4aQyuoGe3TdDa9GLRXMj7vn4qTWvinw)

In this example, you just add the word vectors of a document to get the document vector. So in summary you should now be familiar with the following concepts:

![](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/UjIkIfH2SjeyJCHx9jo3ug_90fa420d7079487b85bac3915d4db5dc_Screen-Shot-2021-02-22-at-1.44.53-PM.png?expiry=1756080000000&hmac=1WgoXKuDnRPPbXT1RaGUieCvfpbgrtsmGyzFoz1q1c8)

Good luck with the programming assignment!