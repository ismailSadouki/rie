
# Autoregressive prediction
. This process repeats until a `<stop>` or end of sentence `<eos>` token is generated.
However, there is a limit to the number of tokens that the model can store in its
memory to generate the next token. This token limit is referred to as the model’s
context window, which is an important factor to consider during the model
selection stage for your GenAI services.
If the context window limit is reached, the model simply discards the least
recently used tokens. This means it can forget the least recently used sentences in documents or messages in a conversation.

**NOTE**
	At the time of writing, the context of the least expensive OpenAI gpt-4o-mini model is
	around ~128,000 tokens, equivalent to more than 300 pages of text.
	The largest context window as of March 2025 belongs to Magic.Dev LTM-2-mini with 100
	million tokens. This equals ~10 million lines of code of ~750 novels.
	The context window of other models falls in the range of hundreds of thousands of tokens.

Short windows will lead to loss of information, difficulty maintaining
conversations, and reduced coherence with the user query.
On the other hand, long context windows have larger memory requirements and
can lead to performance issues or slow services when scaling to thousands of
concurrent users who are using your service. 

 In addition, you will need to consider the costs of relying on models with larger context windows as they tend to be more expensive due to increased compute and memory requirements. The correct choice will depend on your budget and user needs in your use case.

# Integrating a language model into your application

**TINYLLAMA**
	TinyLlama can’t generate more than a few sentences at a time. You will also
need around 3 GB of disk space and RAM to load this model onto memory
for inference

![[260812_18h12m52s_screenshot.png]]

![[260812_18h13m18s_screenshot.png]]



**Hallucinations**
	refer to outputs that aren’t grounded in the training data or reality.
Even though open source SLMs such as TinyLlama have been trained on
impressive number of tokens (3 trillion), a small number of model parameters
may have restricted their ability to learn the ground truth in data. Additionally,
some unfiltered training data may also have been used, both of which can
contribute to more instances of hallucinations.


# Connecting FastAPI with Streamlit UI generator


![[260812_18h35m31s_screenshot.png]]
![[260812_18h36m15s_screenshot.png]]

**Warning**
	while the solution in Example 3-3 is great for prototyping and testing models, it is not suitable for production workloads where several users would need simultaneous access to the model.
	This is because with the current setup, the model is loaded and unloaded onto memory every time a request is processed. Having to load/unload a large model to and from memory is slow and I/O blocking.






# Audio Models
One of the most capable text-to-speech and text-to-audio models is the Bark
model created by Suno AI. This transformer-based model can generate realistic
multilingual speech and audio including music, background noise, and sound
effects.

The Bark model consists of four models chained together as a pipeline to
synthesize audio waveforms from textual prompts, as shown in Figure 3-15




![[260812_18h39m57s_screenshot 1.png]]
<!--⚠️Imgur upload failed, check dev console-->

1. Semantic text model
		A causal (sequential) autoregressive transformer model accepts tokenized input text and captures the meaning via semantic tokens. Autoregressive models predict future values in a sequence by reusing their own previous outputs.
2. Coarse acoustics model
		A causal autoregressive transformer receives the semantic model’s outputs and generates the initial audio features, which lack finer details. Each prediction is based on past and present information in the semantic token sequence.
3. Fine acoustics model
		A noncausal auto-encoder transformer refines the audio representation by generating the remaining audio features. As the coarse acoustics model has generated the entire audio sequence, the fine model doesn’t need to be casual.
4. Encodec audio codec model
		The model decodes the output audio array from all previously generated
		audio codes.


Bark synthesizes the audio waveform by decoding the refined audio features into
the final audio output in the form of spoken words, music, or simple audio
effects.



![[260812_18h46m03s_screenshot.png]]



# Vision Models
One of the most popular architectures used to train image models is called Stable Diffusion (SD).
SD models are trained to encode input images into a latent space. This latent
space is the mathematical representation of patterns in the training data that the
model has learned. If you try to visualize an encoded image, all you would see is
a white noise image, similar to the black and white dots you would see on your
TV screen when it loses signal.

Figure 3-17 shows the full process for training and inference and visualizes how
images are encoded and decoded via the forward and reverse diffusion
processes. A text encoder using text, images, and semantic maps assists in
controlling the output via the reverse diffusion.

![[260812_18h49m58s_screenshot.png]]

What makes these models magical is their ability to decode noisy images back
into original input images. Effectively, the SD models also learn to remove white
noise from an encoded image to reproduce the original image. The model
performs this denoising process over several iterations.

However, you don’t want to re-create images you already have. You will want
the model to create new, never-before-seen images. But how can an SD model
achieve this for you? The answer lies in the latent space where the encoded noisy images live. You can change the noise in these images so that when the model denoises them and decodes them back, you get a whole new image that the model has never seen before.

A challenge remains: how can you control the image generation process so that
the model doesn’t produce random images? The solution is to also encode image
descriptions alongside the image. The patterns in the latent space are then
mapped to textual image descriptions of what is seen in each input image. Now,
you use textual prompts to sample the noisy latent space such that the produced
output image after the denoising process is what you want.

This is how SD models can generate new images that they’ve never seen before
in their training data. In essence, these models navigate a latent space that
contains encoded representations of various patterns and meanings. The model
iteratively refines this noise through a denoising process to produce a novel
image not present in its training dataset.

To download an SD model, you will need to have the Hugging Face diffusers
library installed:
```
$ pip install diffusers
```


![[260812_18h55m00s_screenshot.png]]

**NOTE**
	Vision models are extremely resource hungry. To load and use a small vision model such as TinySD on CPU, you will need around 5 GB of disk space and RAM. However, you can install accelerate using pip install accelerate to optimize resources required so that the model pipeline uses lower CPU memory usage. When serving video models, you will need to use a GPU. Later in this chapter, I will show you how to leverage GPUs for video models.



# Strategies for Serving Generative AI Models

in a production scenarios, you may want to
use larger models to produce higher-quality results that may run only on GPUs
and require a significant amount of video random access memory (VRAM).
In addition to leveraging GPUs, you will need to pick a model-serving strategy
from several options:
**Be model agnostic**
	Load models and generate outputs on every request (useful for model
swapping).
**Be compute efficient**
	Use the FastAPI lifespan to preload models that can be reused for every
request.
**Be lean**
	Serve models externally without frameworks or work with third-party model
APIs and interact with them via FastAPI.

## Be Model Agnostic: Swap Models on Every Request
In the previous code examples, you defined the model loading and generation
functions and then used them in route handler controllers. Using this serving
strategy, FastAPI loads a model into RAM (or VRAM if using a GPU) and runs
a generation process. Once FastAPI returns the results, the model is then
unloaded from RAM. The process repeats for the next request.

As the model is unloaded after use, the memory is released to be used by another
process or model. With this approach, you dynamically swap various models in a
single request if processing time isn’t a concern. This means other concurrent
requests must wait before the server responds to them.

When serving requests, FastAPI will queue incoming requests and process them
in a first in first out (FIFO) order. This behavior will lead to long waiting times as a model needs to be loaded and unloaded every time. In most cases, this
strategy is not recommended, but if you need to swap between multiple large
models and you don’t have sufficient RAM, then you can adopt this strategy for
prototyping. However, in production scenarios, you should never use this
strategy for obvious reasons—your users will want to avoid the long wait times.
![[260812_20h45m33s_screenshot.png]]


## Be Compute Efficient: Preload Models with the FastAPI Lifespan
The most compute-efficient strategy for loading models in FastAPI is to use the
application lifespan. With this approach, you load models on application startup
and unload them on shutdown. During shutdown, you can also undertake any
cleanup steps required, such as filesystem cleanup or logging.

 You can load a heavy model once and then make generations on every request coming using a preloaded model.
 As a result, you will save several minutes in processing time in exchange for a
significant chunk of your RAM (or VRAM if using GPU).  


![[260812_20h48m13s_screenshot.png]]
![[260812_20h48m44s_screenshot.png]]
if you start the application now, you should immediately see model pipelines
being loaded onto memory. Before you applied these changes, the model
pipelines used to load only when you made your first request.

> [!warning]
> You can preload more than one model into memory using the lifespan model-serving strategy, but this isn’t practical with large GenAI models. Generative models can be resource hungry, and in most cases you’ll need GPUs to speed up the generation process. The most powerful consumer GPUs ship with only 24 GB of VRAM. Some models require 18 GB of memory to perform inference, so try to deploy models on separate application instances and GPUs instead.

## Be Lean: Serve Models Externally
Another strategy to serve GenAI models is to package them as external services
via other tools. You can then use your FastAPI application as the logical layer
between your client and the external model server. In this logical layer, you can
handle coordination between models, communication with APIs, management of users, security measures, monitoring activities, content filtering, enhancing
prompts, or any other required logic.

**Cloud providers**
	Cloud providers are constantly innovating serverless and dedicated compute
	solutions that you can use to serve your models externally. For instance, Azure 	Machine Learning Studio now provides a PromptFlow tool that you can use to 	deploy and customize OpenAI or open source language models. Upon 	deployment, you will receive a model endpoint run on your Azure compute 	ready for usage. However, there is a steep learning curve in using PromptFlow or similar tools as they may require particular dependencies and nontraditional steps to be followed.
**BentoML**
	Another great contender for serving models external to FastAPI is BentoML.
	BentoML is inspired by FastAPI but implements a different serving strategy,
	purpose built for AI models.
	A huge improvement over FastAPI for handling concurrent model requests is
	BentoML’s ability to run different requests on different worker processes. It can 	parallelize CPU-bound requests without you having to directly deal with Python 	multiprocessing. On top of this, BentoML can also batch model inferences such that the generation process for multiple users can be done with a single model 	call.


![[260812_20h57m55s_screenshot.png]]
Your FastAPI server can now become a client with the model being served
externally. You can now make HTTP POST requests from within FastAPI to get a
response, as shown in Example 3-19.
![[260812_20h58m49s_screenshot.png]]

**Model providers**
Aside from BentoML and cloud providers, you can also use external model
service providers such as OpenAI. In this case, your FastAPI application
becomes a service wrapper over OpenAI’s API.


**LANGCHAIN**
	You can use the langchain library to switch integration with any LLM
providers. The library also provides excellent tools working with LLMs,
which we will cover later in the book. First, install the library:
	`$ pip install langchain langchain-openai

![[260812_21h01m03s_screenshot.png]]

# The Role of Middleware in Service Monitoring
You can implement a simple monitoring tool where prompts and responses can
be logged alongside their request and response token usage. To implement the
logging system, you can write a few logging functions inside your model-serving
controller. However, if you have multiple models and endpoints, you may benefit
from leveraging the FastAPI middleware mechanism.

Middleware is an essential block of code that runs before and after a request is
processed by any of your controllers. You can define custom middleware that
you then attach to any API route handlers. Once the requests reach the route
handlers, the middleware acts as an intermediary, processing the requests and
responses between the client and server controller.

Excellent uses cases for middleware include logging and monitoring, rate
limiting, content filtering, and cross-origin resource sharing (CORS)
implementations.

<!-![[260812_21h09m03s_screenshot.png]]
![[260812_21h09m11s_screenshot.png]]

> [!warning] USAGE LOGGING VIA CUSTOM MIDDLEWARE IN PRODUCTION
> Don’t use Example 3-22 in production as the monitoring logs can disappear if you run the application from a Docker container or a host machine that can be deleted or restarted without a mounted persistent volume or logging to a database.
> n Chapter 7, you will integrate the monitoring system with a database to persist logs outside
the application environment.

Middleware is a powerful system for executing blocks of code before requests
are passed to the route handlers and before responses are sent to the user. You
saw an example of how middleware can be used to log model usage for any
model-serving endpoint.










































--- 


why use streamlit with fast api? and why not just streamlit?



