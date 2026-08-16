
In this chapter, you will learn more about the role and benefits of asynchronous
programming in boosting the performance and scalability of your GenAI
services. As part of this, you’ll learn to manage concurrent user interactions and
interface with external systems such as databases, implement RAG, and read
web pages to enrich the context of model prompts. You’ll acquire techniques for
effectively dealing with I/O-bound and CPU-bound operations, especially when
dealing with external services or handling long-running inference tasks.

We will also dive into strategies for efficiently handling long-running Generative
AI inference tasks, including the use of FastAPI event loop for background tasks
execution.

# Optimizing GenAI Services for Multiple Users
AI workloads are computationally expensive operations that can inhibit your
GenAI services from serving multiple simultaneous requests. In most production
scenarios, multiple users will be using your applications. Therefore, your
services will be expected to serve requests concurrently such that multiple
overlapping tasks can be executed. However, if you’re interfacing with GenAI
models and external systems such as databases, the filesystem, or the internet,
there will be operations that can block other tasks from executing on your server.
Long-running operations that can halt the program execution flow are considered
blocking.

These blocking operations can be twofold:
**Input/output (I/O) bound**
	Where a process has to wait because of data input/output operation, which
	can come from a user, file, database, network, etc. Examples include reading
	or writing a file to a disk, making network requests and API calls, sending or
	receiving data from databases, or waiting for user input.
**Compute bound**
	Where a process has to wait because of a compute-intensive operation on
	CPU or GPU. Compute-bound programs push the CPU or GPU cores to their
	limit by performing intensive computations, often blocking them from
	performing other tasks.

You have a few strategies to serve multiple users:
**System optimization**
	For I/O-bound tasks like fetching data from a database, working with files on
	disk, making network requests, or reading web pages
**Model optimization**
	For memory- and compute-bound tasks such as model loading and inference
**Queuing system**
	For handling long-running inference tasks to avoid delays in responding
 
 dive deeper into the topic of concurrency and parallelism as understanding both concepts will help you identify the correct strategies to use for your own use
cases


**Concurrency** refers to the ability of a service in handling multiple requests or
tasks at the same time, without completing one after another. During concurrent
operations, the timeline of multiple tasks can overlap and may start and end at
different times.
In Python, you can implement concurrency with a single CPU core by switching
between tasks on a single thread (via asynchronous programming) or across
different threads (via multithreading).
> [!note] TIME SLICING
> Time slicing is a scheduling mechanism in multithreading and asynchronous
programming where a process allocates CPU time between tasks to give the
illusion of concurrent execution.
>In Python, the CPU time can be allocated to only one task at any moment
because of Python’s Global Interpreter Lock (GIL). Python’s GIL allows
only one thread (i.e., execution flow in a Python process) to control the
Python interpreter for executing code. This means that only one thread can be in a state of execution at any point in time.
>The GIL was originally implemented to simplify Python’s development, ease
memory management between threads in a Python process, and prioritize the
performance of single-threaded programs. It was also added to Python to
ensure thread-safety within the process, preventing race conditions that
could lead to data corruption when working with shared resources between
threads.
>However, the addition of the GIL to Python also means that true parallel
execution of threads in a single Python process is not possible. When a task
is waiting for an I/O operation to finish, the CPU can quickly switch to
another task to avoid blocking other operations. The paused tasks save their
state and can resume once the I/O operations are done.

With multiple cores, you can also implement a subset of concurrency called
parallelism where tasks are split among several independent workers (via
multiprocessing), with each executing tasks simultaneously on their own isolated
resources and separate processes.

**NOTE**
	Although there are plans to remove the GIL from Python soon, at the time of this writing it is not possible for multiple threads to simultaneously work through tasks. Therefore, concurrency on a single core can give an illusion of parallelism even though there is one process doing all the work. The single process can only multitask by switching active threads to minimize waiting times of I/O blocking operations.
	You can only achieve true parallelism with multiple workers (in multiprocessing).
Even though concurrency and parallelism have many similarities, they aren’t
exactly the same concepts. The big difference between them is that concurrency
can help you manage multiple tasks by interleaving their execution, which is
useful for I/O-bound tasks. Parallelism, on the other hand, involves executing
multiple tasks simultaneously, typically on multicore machines, which is more
useful for CPU-bound tasks.

You can implement concurrency using approaches like threading or   programming (i.e., time-slicing on a single-core machine, where tasks are interleaved to give the appearance of simultaneous execution).

![[260812_23h48m59s_screenshot.png]]

Imagine that you’re visiting a fast-food restaurant and placing an order. In a
concurrent system, you’ll see the restaurant owner taking orders while cooking
burgers, attending to each task time to time, and effectively multitasking by
switching between tasks. In a parallel system, you’ll see multiple staff members
taking orders and a few others cooking the burgers at the same time. Here
different workers handle each task simultaneously.

Without any multithreading or asynchronous programming in a single-threaded
process, the process has to wait for blocking operations to finish before it can
start new tasks. Without multiprocessing implementing parallelism on multiple
cores, computationally expensive operations can block the application from
starting other tasks.




Figure 5-2 shows the distinctions between nonconcurrent execution, concurrent
execution without parallelism (single core), and concurrent execution with
parallelism (multiple cores)
**No concurrency (synchronous)**
	A single process (on one core) executes tasks sequentially.
![[260812_23h52m13s_screenshot.png]]

**Concurrent and non-parallel**
	Multiple threads in a single process (on a core) handle tasks concurrently but not in parallel due to Python’s GIL.
![[260812_23h53m15s_screenshot.png]]

**Concurrent and parallel**
	Multiple processes on multiple cores perform the tasks in parallel, making the most of multicore processors for maximum efficiency.
![[260812_23h53m55s_screenshot.png]]

In multiprocessing, each process has access to its own memory space and
resources to complete a task in isolation from other processes. This isolation can make processes more stable—since if a process crashes, it won’t affect others—
but makes inter-process communication more complex compared to threads,
which share the same memory space, as shown in Figure 5-3.

![[260812_23h56m11s_screenshot.png]]

Distributed workloads often use a managing process that coordinates the
execution and collaboration of these processes to avoid issues such as data
corruption and duplicating work. A good example of multiprocessing is when
you serve requests with a load balancer managing traffic to multiple containers,
each running an instance of your application.

Both multithreading and asynchronous programming reduce wait time in I/O
tasks because the processor can do other work while waiting for I/O. However,
they don’t help with tasks that require heavy computation, like AI inference,
because the process is busy with computing some results.<mark> Therefore, to serve a large self-hosted GenAI model to multiple users, you should either scale services with multiprocessing or use algorithmic model optimizations (via specialized model inference servers like vLLM).</mark>


<mark>Your first instinct when working with slow models may be to adopt parallelism
by creating multiple instances of your FastAPI service (multiprocessing) in a
single machine to serve requests in parallel.</makr>

Unfortunately, multiple workers running in separate processes will not have
access to a shared memory space. As a result, you can’t share artifacts—like a
GenAI model—loaded in memory between separate instances of your app in
FastAPI. Sadly, a new instance of your model will also need to be loaded, which
will significantly eat up your hardware resources. This is because FastAPI is a general-purpose web server that doesn’t natively optimize serving GenAI
models.
The solution is not parallelism on its own, but to adopt the external model-
serving strategy, as discussed in Chapter 3.

The only instance where you can treat AI inference workloads as I/O-bound,
instead of compute-bound, is when you’re relying on third-party AI provider
APIs (e.g., OpenAI API). In this case, you’re offloading the compute-bound
tasks to the model provider through network requests.
On your side, the AI inference workloads become I/O-bound through network
requests, allowing for the use of concurrency through time slicing. The third-
party provider has to worry about scaling their services to handle model
inferences—that are compute-bound—across their hardware resources.

<mark> You can externalize the serving and inference of larger GenAI models such as an
LLM, with specialized servers like vLLM, Ray Serve, or NVIDIA Triton.</mark>

Later in this chapter, I will detail how these servers maximize inference
efficiency of compute-bound operations during model inference while
minimizing the model’s memory footprint during the data generation process.

To help you digest what was discussed so far, have a look at the comparison
table of concurrency strategies in Table 5-1 to understand when and why to use
each.

![[260813_00h05m22s_screenshot.png]]
![[260813_00h05m55s_screenshot.png]]
![[260813_00h06m32s_screenshot.png]]
![[260813_00h07m19s_screenshot.png]]
![[260813_00h07m54s_screenshot.png]]
![[260813_00h08m34s_screenshot.png]]

 let’s continue by enhancing your services with asynchronous programming to efficiently manage I/O-bound operations. Later we’ll focus on optimizing compute-bound tasks, specifically model inference via specialized servers.
# Optimizing for I/O Tasks with Asynchronous Programming
In this section, we’ll explore the use of asynchronous programming to prevent
blocking the main server process with I/O-bound tasks during AI workloads

#### Synchronous Versus Asynchronous (Async) Execution
An application is considered synchronous when tasks are performed in a
sequential order with each task waiting for the previous one to complete before
starting. For applications that run infrequently and take only a few seconds to
process, synchronous code rarely causes a problem and can make
implementations faster and easier. However, if you need concurrency and want
the efficiency of your services to be maximized on every core, your services
should multitask without waiting for blocking operations to complete. That’s
where implementing asynchronous (async) concurrency can help.

Let’s look at a few examples of synchronous and async functions to understand
how much of a performance boost an async code can give you

![[260813_10h10m39s_screenshot.png]]
Calling task() three times in Example 5-1 takes 15 seconds to complete as
Python waits for the blocking operation sleep() to complete.

To develop async programs in Python, you can use the asyncio package as part
of the standard library of Python 3.5 and later versions. Using asyncio,
asynchronous code looks similar to sequential synchronous code but with
additions of async and await keywords to perform nonblocking I/O operations.

![](https://i.imgur.com/dkCQifm.png)


After running Example 5-2, you will notice that the task() function was
concurrently called three times. On the other hand, the code in Example 5-1 calls
the task() function three times sequentially. The async function ran inside the
asyncio’s event loop, which was responsible for executing the code without
waiting.

![](https://i.imgur.com/dxS7zHW.png)


With asynchronous I/O:

```txt
A: ███ wait █████████
B:     ███ wait █████████
C:         ███ wait █████████

Total ≈ 2 seconds
```

> [!caution] Another common pitfall is using blocking code that’s not asynchronous within an async function that will inadvertently prevent Python from doing other tasks while waiting.


At the heart of asyncio lies a first-class object called an event loop, responsible
for efficient handling of I/O events, system events, and application context
changes.

The central component of `asyncio` is the **event loop**.


```txt
                FastAPI
                   │
              Event Loop
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
   Request A   Request B   Request C
       │           │           │
       ▼           ▼           ▼
    DB wait     API wait     DB wait
       │           │           │
       └───────────┼───────────┘
                   │
              continue work
```

The event loop continuously asks:

> "Which coroutine is ready to make progress?"
![](https://i.imgur.com/cKVh6HG.png)

The event loop can be compared to a while True loop that watches for events
or messages emitted by coroutine functions within the Python process and
dispatches events to switch between functions while waiting for I/O blocking
operations to complete. This orchestration allows other functions to execute
asynchronously without interruption.

![](https://i.imgur.com/zUjOGUc.png)



## Async Programming with Model Provider APIs
et’s look at a real-world scenario related to building GenAI services where you need to use a model provider’s API—such as OpenAI, Anthropic, or Mistral—since it may be more expensive to serve LLMs yourself.

When you use a provider’s API, you no longer have CPU-bound AI workloads
to worry about since they become I/O-bound for you, and you offload the CPU-
bound workloads to the provider. Therefore, it makes sense to know how to
leverage async programming to concurrently interact with the model provider’s
API.

The good news is API owners will often release both synchronous and
asynchronous clients and software development kits (SDKs) to reduce the work
needed to interact with their endpoints.
![](https://i.imgur.com/PGyzv3z.png)
	![](https://i.imgur.com/XmnkPiv.png)
	The event loop remains free while the thread performs the blocking operation.
	![](https://i.imgur.com/ngmOmJh.png)
	![](https://i.imgur.com/UKGJa4s.png)
	![](https://i.imgur.com/NOvWzoa.png)
In Example 5-4, you will interact with OpenAI GPT-3.5 API via both
synchronous and asynchronous OpenAI clients to understand the performance
difference between the two.
![](https://i.imgur.com/qe9NLFD.png)
The difference between the sync and async clients is that with the async version,
FastAPI can start processing user inputs in parallel without waiting for a
response from the OpenAI API for the previous user input.
![](https://i.imgur.com/tse5bQO.png)


Here are some common pitfalls and problems you might face with async code:
- Understanding and debugging errors can be more complex due to the
nonlinear execution flow of concurrent tasks.
- Some libraries, like aiohttp, require nested async context managers for
proper implementation. This can get confusing pretty fast.
- Mixing asynchronous and synchronous code can negate any
performance benefits, such as if you forget to mark functions with the
async and await keywords.
- Not using async-compatible tools and libraries can also cancel out any
performance benefits; for example, using the requests package instead
of aiohttp for making async API calls.
- Forgetting to await coroutines within any async function or awaiting
non-coroutines can lead to unexpected behavior. All async keywords
must be followed by an await.
- Improperly managing resources (e.g., open API/database connections or
file buffers) can cause memory leaks that freeze your computer. You can
also leak memory if you don’t limit the number of concurrent operations
in async code.
- You might also run into concurrency and race condition issues where
the thread-safety principle is violated, causing deadlocks on resources
leading to data corruption.


## Event Loop and Thread Pool in FastAPI
Under the hood, FastAPI can handle both async and sync blocking operations. It
does this by running sync handlers in its thread pool so that blocking operations
don’t stop the event loop from executing tasks.
using ASGI, the FastAPI server supports concurrency via both
multithreading (via a thread pool) and asynchronous programming (via an event
loop) to serve multiple requests in parallel, while keeping the main server
process from being blocked.
FastAPI sets up the thread pool by instantiating a collection of threads at
application startup to reduce the runtime burden of thread creation.4 It then
delegates background tasks and synchronous workloads to the thread pool to
prevent the event loop from being blocked by any blocking operations inside the
synchronous handlers. The event loop is also referred to as the main FastAPI
server thread that is responsible for orchestrating the processing of requests.

. In FastAPI, the event loop is also responsible for orchestrating the asynchronous processing of requests.

imagine if multiple concurrent users were using both the synchronous and
asynchronous OpenAI GPT-3.5 handlers (endpoints) of your FastAPI service, as
shown in Example 5-4. FastAPI will run the async handler requests on the event
loop since that handler uses a nonblocking async OpenAI client. On the other
hand, FastAPI has to delegate the synchronous handler requests to the thread
pool to protect the event loop from blocking. Since delegating requests (to
threads) and switching between threads in a thread pool is more work, the
synchronous requests will finish later than their async counterparts.


```txt
                 ASYNC APPLICATION
                       │
                       ▼
                 ┌───────────┐
                 │ Event Loop│
                 └─────┬─────┘
                       │
          ┌────────────┴────────────┐
          │                         │
       async I/O              blocking code
          │                         │
          ↓                         ↓
   handled by event loop       Thread Pool
                                    │
                                    ↓
                             synchronous function
```

![](https://i.imgur.com/GEZ4px5.png)
![](https://i.imgur.com/ybARgHt.png)
def.
However, keep in mind that when you declare handlers with async def,
FastAPI trusts you with performing only nonblocking operations. When you
break that trust and execute blocking operations inside async routes, the event
loop will be blocked and can no longer continue with executing tasks until the
blocking operation is finished.

#### Blocking the Main Server
If you’re using the async keyword when defining your functions, make sure
you’re also using the await keyword somewhere inside your function and that
none of the package dependencies you use inside the function are synchronous.

Avoid declaring route handler functions as async if their implementation is
synchronous. Otherwise, requests to the affected route handlers will block the
main server from processing other requests while the server is waiting for the
blocking operation to complete. It won’t matter if the blocking operation is I/O-
bound or compute-bound. Therefore, any calls to databases or AI models can
still cause the blockage if you’re not careful.
![](https://i.imgur.com/2WaSZrD.png)
The request won’t block the main thread and doesn’t need to be handed off to the
thread pool. As a result, the FastAPI event loop can process the request much
faster using the async OpenAI client

You now should feel more comfortable implementing new features in your
FastAPI service that require performing I/O-bound tasks.





# Project: Talk to the Web (Web Scraper)
The process for building a simple asynchronous scraper is as follows:
1. evelop a function to match URL patterns using regex on user prompts
to the LLM.
2. If found, loop over the list of provided URLs and asynchronously fetch
the pages. We will use an asynchronous HTTP library called aiohttp
instead of the requests since requests can only make synchronous
network requests.
3. Develop a parsing function to extract the textual content from fetched
HTML.
4. Feed the parsed page content to the LLM alongside the original user
prompt.


![](https://i.imgur.com/erHN50s.png)
you now have the utility scraper functions you need to implement the web
scraping feature in your /generate/text endpoint.

Next, upgrade the text-to-text handler to use the scraper functions via a
dependency in an asynchronous manner, as shown in Example 5-7
![](https://i.imgur.com/HpbALuZ.png)
![](https://i.imgur.com/bMxbAaE.png)


Now, run the Streamlit client in the browser and try your shiny new feature.
Figure 5-6 shows my experiment.


# Project: Talk to Documents (RAG)

In this project, we will build a RAG module into your GenAI service to give you
a hands-on experience interacting asynchronously with external systems such as
a database and a filesystem.
![](https://i.imgur.com/AqUxlgE.png)

The pipeline for RAG consists of the following stages
1. Extraction of documents from a filesystem to load the textual content in
chunks onto memory.
2. Transformation of the textual content by cleaning, splitting, and
preparing them to be passed into an embedding model to produce
embedding vectors that represent a chunk’s semantic meaning.
3. Storage of embedding vectors alongside metadata, such as the source
and text chunk, in a vector store such as Qdrant.
4. Retrieval of semantically relevant embedding vectors by performing a
semantic search on the user’s query to the LLM. The original text
chunks—stored as metadata of the retrieved vectors—are then used to
augment (i.e., enhance the context within) the initial prompt provided to
the LLM.
5. Generation of LLM response bypassing both the query and retrieved
chunks (i.e., context) to the LLM for getting a response.

![](https://i.imgur.com/vMgHv5S.png)
 Figure 5-9 shows the system architecture of a “talk to your documents”
service enabled with RAG.
![](https://i.imgur.com/gdLtoTM.png)
Using FastAPI’s UploadFile class, you can accept documents from users in
chunks and save them into the filesystem or any other file storage solution such
as a blob storage. The important item to note here is that this I/O operation is
nonblocking through asynchronous programming, which FastAPI’s UploadFile
class supports.


![](https://i.imgur.com/1egTuz3.png)







With upload functionality implemented, you can now turn your attention to
building the RAG module. Figure 5-11 shows the detailed pipeline, which opens
![](https://i.imgur.com/yuTD997.png)
As you can see in Figure 5-11, you need to asynchronously fetch the stored files
from the hard disk and pass them through a data transformation pipeline prior to
storage via an asynchronous database client
The data transformation pipeline consists of the following parts:
**Extractor**
	Extract content of PDFs and store in text files back onto the hard disk.
**Loader**
	Asynchronously load a text file into memory in chunks.
**Cleaner**
	Remove any redundant whitespace or formatting characters from text chunks
**Embedder**
	Use a pretrained and self-hosted embedding model to convert text into
embedding vectors.

![](https://i.imgur.com/D3YT8SG.png)
Once users upload their PDF files onto your server’s filesystem via the process
shown in Example 5-8, you can immediately convert them into text files via the
pypdf library. Since there is no asynchronous library for loading binary PDF
files, you will want to convert them into text files first
Example 5-9 shows how to load PDFs, extract and process their content, and
then store them as text files
![](https://i.imgur.com/gWmQ4b4.png)


Example 5-10 shows the implementation of the RAG data transformation
pipeline including the async text loader, cleaner, and embedding functions





---


what is the difference between a thread and a core

what is concurrency and parallelism in python
multithreading, asynchronous programming, multiprocessing, workloads

The text extractor will convert the PDF files into simple text files that we can
stream into memory in chunks using an asynchronous file loader. Each chunk
can then be cleaned and embedded into an embedding vector using an open
source embedding model such as jinaai/jina-embeddings-v2-base-en,
available to download from the Hugging Face model hub.

![](https://i.imgur.com/DE9tnYy.png)

Once the data is processed into embedding vectors, you can store them into the
vector database.
![](https://i.imgur.com/oHyBeYV.png)

The following code examples require you to run a local instance of the qdrant
vector database on your local machine for the RAG module. Having a local
database setup will give you the hands-on experience of working asynchronously
with production-grade vector databases. To run the database in a container, you
should have Docker installed on your machine and then pull and run the qdrant
vector database container.
```
$ docker pull qdrant/qdrant
$ docker run -p 6333:6333 -p 6334:6334 \
-v $(pwd)/qdrant_storage:/qdrant/storage:z \
qdrant/qdrant
```
Since database storage and retrieval are I/O operations, you should use an
asynchronous database client. Thankfully, qdrant provides an asynchronous
database client to work with
instead of writing several functions to store and retrieve data from the database,
you can use the repository pattern mentioned in Chapter 2. With the repository
pattern, you can abstract low-level create, read, update, and delete database
operations with defaults that match your use case.


Example 5-11 shows the repository pattern implementation for the Qdrant vector
database
![](https://i.imgur.com/RiUL0x2.png)
![](https://i.imgur.com/ptZMIle.png)
The VectorRepository class should now make it easier to interact with the
database.

When storing vector embeddings, you will also store some metadata including
the name of the source document, the location of the text within source, and the
original extracted text. RAG systems rely on this metadata to augment the LLM
prompts and to show source information to the users.

You can now extend the VectorRepository and create the VectorService that
allow you to chain together the data processing and storage pipeline, as shown in
![](https://i.imgur.com/KCeBhLp.png)
The final step in the RAG data processing and storage pipeline is to run the text
extraction and storage logic within the file_upload_controller as
background tasks. The implementation is shown in Example 5-13 so that the
handler can trigger both operations in the background after responding to the
user.
![](https://i.imgur.com/XlVX4jk.png)
![](https://i.imgur.com/Sa5fr2F.png)
After building the RAG data storage pipeline, you can now focus on the search-
and-retrieval system, which will allow you to augment the user prompts to the
LLM, with knowledge from the database. Example 5-14 integrates the RAG
search-and-retrieval operations with the LLM handler to augment the LLM
prompts with additional context.

![](https://i.imgur.com/9DMUlYh.png)
![](https://i.imgur.com/mgAgd1L.png)
Congratulations! You now have a fully working RAG system enabled by open
source models and a vector database.

You can work on improving the RAG module further by implementing various
other techniques, which I will not cover in this book:
- Optimize text splitting, chunk sizing, cleaning and embedding
operations.
- Perform query transformations using the LLM to aid the retrieval and
augmentation system via techniques such as prompt compression,
chaining, refining, and aggregating, etc., to reduce hallucinations and
improve LLM performance.
- Summarize or break down large augmented prompts to feed the context
into the models using a sliding window approach.
- Enhance retrieval algorithms to handle ambiguous queries and
implement fallback mechanisms for incomplete data.
- Enhance the retrieval performance with methods such as maximal
marginal relevance (MMR) to enrich the augmentation process with
more diverse documents.
- Implement other advanced RAG techniques like retrieval reranking and
filtering, hierarchical database indices, RAG fusion, retrieval
augmented thoughts (RAT), etc., to improve the overall generation
performance.


---






# Optimizing Model Serving for Memory- and Compute-Bound AI Inference Tasks

Using async tools and techniques, your service remained responsive when
interacting with the web, the filesystem, and databases. However, if you’re self-
hosting the model, switching to async programming techniques won’t fully
eliminate the long waiting times. This is because the bottleneck will be model
inference operations.

### Compute-Bound Operations
ou can speed up the inference by running models on GPUs to massively
parallelize computations. Modern GPUs have staggering compute power
measured by the number of floating-point operations per second (FLOPS), with
modern GPUs reaching teraflops (NVIDIA A100) or petaflops (NVIDIA H100)
of compute. However, despite their significant power and parallelization
capabilities, modern GPU cores are often underutilized under concurrent
workloads with larger models.

<mark>When self-hosting models on GPUs, model parameters are loaded from disk to
RAM (I/O bound) and then moved from RAM to the GPU high-bandwidth
memory by the CPU (memory bound). Once model parameters are loaded on the
GPU memory, inference is performed (compute bound).</mark>



Counterintuitively, model inference for larger GenAI models such as SDXL and
LLMs is not I/O- or compute-bound, but rather memory-bound. This means it
takes more time to load 1 MB of data into GPU’s compute cores than it takes for
those compute cores to process 1 MB of data. Inevitably, to maximize the
concurrency of your service, you will need to batch the inference requests and fit
the largest batch size you can into the GPU high-bandwidth memory.

```
larger feasible batch size  ----→  better throughput
```
Therefore, even when using async techniques and latest GPUs, your server can
be blocked waiting for billions of model parameters to be loaded to the GPU
high-bandwidth memory during each request. To avoid blocking the server, you
can decouple the memory-bound model-serving operations from your FastAPI
server by externalizing model serving, as we touched upon in Chapter 3.

Let’s see how to delegate model serving to another process.

### Externalizing Model Serving
You have several options available to you when externalizing your model-
serving workloads. You can either host models on another FastAPI server or use
specialized model inference servers.

For instance, if you need to self-host LLMs, LLM-serving frameworks can perform several inference optimizations for you such as batch processing, tensor parallelism, quantization, caching, streaming outputs, GPU memory management, etc.
![](https://i.imgur.com/Gm8P2W0.png)
![](https://i.imgur.com/U6VPOJ6.png)
vLLM is designed for production inference workloads on NVIDIA GPUs in Linux
environments where the server can delegate requests to multiple GPU cores via tensor parallelism. It does also support distributed computing when scaling services beyond a single machine via its Ray Serve dependency

![](https://i.imgur.com/lfq1MBO.png)
With the vLLM FastAPI server up and running, you can now replace the model-
serving logic in your current service with network calls to the vLLM server.
![](https://i.imgur.com/CPLUAgL.png)
Next, remove the code related to the FastAPI lifespan so that your current
service won’t load the TinyLlama model. You can achieve this by following the
code in Example 5-17.
![](https://i.imgur.com/n5Khh6d.png)
Congratulations, you’ve now achieved concurrency with your AI inference
workloads. You implemented a form of multiprocessing on a single machine by
moving your LLM inference workloads to another server. Both servers are now
running on separate cores with your LLM server delegating work to multiple
GPU cores, leveraging parallelism. This means your main server is now able to
process multiple incoming requests and do other tasks than processing one LLM
inference operation at a time.

> [!note] TIP
> Bear in mind that any concurrency you’ve achieved so far has been limited to a single
machine.
To support more concurrent users, you may need more machines with CPU and GPU cores. At
that point, distributed computing frameworks like Ray Serve and Kubernetes can help to scale
and orchestrate your services beyond a single worker machine using parallelism.

Before integrating vLLM, you would experience long waiting times between
requests because your main server was too busy running inference operations.
With vLLM, there is now a massive reduction in latency and increase in
throughput of your LLM service.

**LATENCY AND THROUGHPUT**
<mark>Latency in the context of LLMs refers to the time taken from when a request
is made to the model until the first response is received. It’s a measure of the
delay experienced during the processing of a single request. Throughput, on
the other hand, is the number of requests that an LLM can process within a
given time frame and indicates the server’s capacity to handle concurrent or
sequential requests over time. Latency can be measured in delay seconds and
throughput in tokens per minute (TPM).</mark>

As a developer, you will want your service to have the lowest latency and
the highest throughput possible. However, there is a trade-off between the
model size and quality versus these two metrics. Normally, LLMs with
larger number of parameters achieve higher quality but also increased
latency and reduced throughput.

Research is currently underway to use model compression techniques such
as distillation, quantization, and pruning to keep language models small
while maintaining high quality, throughput, and small latency in AI
inference services.

In addition to model compression mechanisms like quantization, vLLM uses
other optimization techniques including continuous request batching, cache
partitioning (paged attention), reduced GPU memory footprint via memory
sharing, and streaming outputs to achieve smaller latency and high throughput.
#### Request batching and continuous batching
LLMs produce the next token prediction in an
autoregressive manner.
![](https://i.imgur.com/giToHPb.png)
This means the LLMs must perform several inference iterations in a loop to
produce a response, and each iteration produces a single output token. The input
sequence grows as each iteration’s output token is appended to the end, and the
new sequence is forwarded to the model in the next iteration step. Once the
model generates an end-of-sequence token, the generation loop stops.
Essentially, the LLM produces a sequence of completion tokens, stopping only
after producing a stop token or reaching a maximum sequence length.


The LLM must calculate several attention maps for each token in the sequence
so that it can iteratively make the next token predictions.


Fortunately, GPUs can parallelize the attention map calculations for each
iteration. As you learned, these attention maps are capturing the meaning and
context of each token within the input sequence and are expensive to calculate.
Therefore, to optimize inference, LLMs use key-value (KV) caching to store
calculated maps in the GPU memory.

<mark>However, storing parameters on the GPU memory for reuse between iterations
can consume huge chunks of GPU memory. For instance, a 13B-parameter
model consumes nearly 1 MB of state for each token in a sequence on top of all
those 13B model parameters. This means there is a limited number of tokens you
can store in memory for reuse.
</mark>

<mark>If you’re using a higher-end GPU, such as the A100 with 40 GB RAM, you can
only hold 14 K tokens in memory at once, while the rest of the memory is used
up for storing 26 GB of model parameters. In short, the GPU memory consumed
scales with the base model size plus the length of the token sequence.
</mark>


<mark>To make matters worse, if you need to serve multiple users concurrently by
batching requests, your GPU memory has to be shared between multiple LLM
inferences. As a result, you have less memory to store longer sequences, and
your LLM is constrained to a shorter context window
 On the other hand, if you
want to maintain a large context window, then you can’t handle more concurrent
users. As an example, a sequence length of 2048 means that your batch size will
be limited to 7 concurrent requests (or 7 prompt sequences). Realistically, this is
an upper-bound limit and doesn’t leave room for storing intermediate
computations, which will reduce the aforementioned numbers even further.</mark>

What this all means is that LLMs are failing to fully saturate the GPU’s available
resources. The primary reason is that a significant portion of the GPU’s memory
bandwidth is consumed in loading the model parameters instead of processing
inputs.

The first step to reduce the load on your services is to integrate the most efficient
models. Often, smaller and more compressed models could do the job you’re
asking of them, with a similar performance to their larger counterparts.

<mark>Another suitable solution to the GPU underutilization problem is to implement
request batching where the model processes multiple inputs in groups, reducing
the overhead of loading model parameters for each request. This is more
efficient in using the chip’s memory bandwidth, leading to higher compute
utilization, higher throughput, and less expensive LLM inference. LLM
inference servers like vLLM take advantage of batching plus fast attention, KV
caching, and paged attention mechanisms to maximize throughput.</mark>

You can see the difference of response latency and throughput with and without
batching in Figure 5-14
![](https://i.imgur.com/qNu51di.png)
There are two ways to implement batching:
**Static batching**
	The size of the batch remains constant.
**Dynamic or continuous batching**
	The size of batch is determined based on demand.
In static batching, we wait for a predetermined number of incoming requests to
arrive before we batch and process them through the model. However, since
requests can finish at any time in a batch, we’re effectively delaying responses to
every request—and increasing latency—until the whole batch is processed.

Releasing the GPU resource can also be tricky when processing a batch and
adding new requests to the batch that may be at different completion states. As a
result, the GPU remains underutilized as the generated sequences within a batch
vary and don’t match the length of the longest sequence in that batch.

Figure 5-15 illustrates static batching in the context of LLM inference.
![](https://i.imgur.com/RcpYYJp.png)
In Figure 5-15 you will notice the white blocks representing underutilized GPU
computation time. Only one input sequence in the batch saturated the GPU
across the batch’s processing timeline.

Aside from adding unnecessary waiting times and not saturating the GPU
utilization, what makes static batching problematic is that users of an LLM-
powered chatbot service won’t be providing fixed-length prompts or expect
fixed-length outputs. The variance in generation outputs could cause massive
underutilization of GPUs.
A solution is to avoid assuming fixed input or output sequences and instead set
dynamic batch sizes during the processing of a batch. In dynamic or continuous
batching, the size of batch can be set based on the incoming request sequence
length and the available GPU resource. With this approach, new generation
requests can be inserted in a batch by replacing completed requests to yield
higher GPU utilization than static batching.

Figure 5-16 shows how dynamic or continuous batching can fully saturate the
GPU resource
![](https://i.imgur.com/xuPMlaQ.png)

While the model parameters are loaded, requests can keep flowing in, and the
LLM inference server schedules and insert them into the batch to maximize GPU
usage. This approach leads to higher throughput and reduced latency.
<mark>If you’re building a LLM inference server, you will probably want to bake in the
continuous batching mechanism into your server.</mark> However, the good news is that
the vLLM server already provides continuous batching out of the box with its
FastAPI server, so you don’t have to implement all of that yourself. Additionally,
it also ships with another important GPU optimization feature, which sets it apart
from other alternative LLM inference frameworks: paged attention.

#### **Paged attention**
Efficient memory usage is a critical challenge for systems that handle high-
throughput serving, particularly for LLMs. For faster inference, today’s models
rely on KV caches to store and reuse attention maps, which grow exponentially
as input sequence lengths increase.
Paged attention is a novel solution designed to minimize the memory demands
of these KV caches, subsequently enhancing the memory efficiency of LLMs
and making them more viable for use on devices with limited resources. In
transformer-based LLMs, attention key and value tensors are generated for each
input token to capture essential context. Instead of recalculating these tensors at
every step, they’re saved in the GPU memory as a KV cache, which serves as the model’s memory. However, the KV cache can grow to enormous sizes, such as
40 GB for a model with 13B parameters, posing a significant challenge for
efficient storage and access, particularly on hardware with constrained resources.
<mark>Paged attention introduces a method that breaks down the KV cache into
smaller, more manageable segments called pages, each holding a KV vector for
a set number of tokens. With this segmentation, paged attention can efficiently
load and access KV caches during the attention computations</mark>
 You can compare
this technique to how the virtual memory is managed by operating systems,
where the logical arrangement of data is separated from its physical storage.
Essentially, a block table maps the logical blocks to physical ones, allowing for
dynamic allocation of memory as new tokens are processed. The core idea is to
avoid memory fragmentation by leveraging logical blocks (instead of physical
ones) and use a mapping table to quickly access data stored in a paged physical
memory.

You can break down the paged attention mechanism into several steps:
**Partitioning the KV cache**
	The cache is split into fixed-size pages, with each containing a portion of the
key-value pairs.
**Building the lookup table**
	A table is created to map query keys to their corresponding pages, facilitating quick allocation and retrieval.
**Selective loading**
	Only the necessary pages for the current input sequence are loaded during
inference, reducing the memory footprint.
**Attention computation**
	The model computes attention using the key-value pairs from the loaded
pages. This approach aims to make LLMs more accessible by addressing the
memory bottleneck, potentially enabling their deployment on a wider range
of devices.

The aforementioned steps enable the vLLM server to maximize memory usage
efficiency through the mapping of physical and logical memory blocks so that
the KV cache is efficiently stored and retrieved during generation.

In a blog post published on Anyscale.com, the authors have researched and
compared the performance of various LLM-serving frameworks during
inference. The authors concluded that leveraging both paged attention and
continuous batching mechanisms are so powerful in optimizing GPU memory
usage that the vLLM server was able to reduce latencies by 4 times and
throughput by up to 23 times

In the next section, we will turn our attention to GenAI workloads that can take a
long time to process and are compute-bound. This is mostly the case with large
non-LLM models such as SDXL where performing batch inferences (such as
batch image generation) for multiple users may prove challenging.

# Managing Long-Running AI Inference Tasks
With the ability to host models in a separate process outside the FastAPI event
loop, you can turn your attention to blocking operations that take a long time to
complete.
In the previous section, you leveraged specialized frameworks such as vLLM to
externally host and optimize the inference workloads of your LLMs. However,
you may still run into models that can take significant time to generate results.
To prevent your users from waiting, you should manage tasks that generate
models and take a long time to complete.

Several GenAI models such as Stable Diffusion XL may take several minutes,
even on a GPU, to produce results. In most cases, you can ask your users to wait
until the generation process is complete. But if users are using a single model
simultaneously, the server will have to queue these requests. When your users
work with generative models, they need to interact with it several times to guide
the model to the results they want. This usage pattern creates a large backlog of
requests, and users at the end of the queue will have to wait a long time before
they see any results.

If there was a way to handle long-running tasks without making the users wait,
that would be perfect. Luckily, FastAPI provides a mechanism for solving these
kinds of problems.
FastAPI’s background tasks is a mechanism you can leverage to respond to users
while your models are busy processing the request. You’ve been briefly
introduced to this feature while building the RAG module where a background
task was populating a vector database with the content of the uploaded PDF
documents.


Using background tasks, your users can continue sending requests or carry on
with their day without having to wait. You can either save the results to disk or a
database for later retrieval or provide a polling system so that their client can
ping for updates as the model processes the requests. Another option is to create
a live connection between the client and the server so that their UI is updated
with the results as soon as it becomes available. All these solutions are doable
with FastAPI’s background tasks.

Example 5-18 shows how to implement background tasks to handle long-
running model inferences.
![](https://i.imgur.com/CtPZohh.png)
In Example 5-18, you’re allowing FastAPI to run inference operations in the
background (via an external model server API) such that the event loop remains
unblocked to process other incoming requests. You can even run multiple tasks
in the background, such as generating images in batches (in separate processes)
and sending notification emails. These tasks are added to a queue and processed
sequentially without blocking the user. You can then store the generated images
and expose an additional endpoint that clients can use to poll for status updates
and to retrieve the inference results.
![](https://i.imgur.com/YVo6Ckt.png)

While FastAPI’s background tasks are a wonderful tool for handling simple
batch jobs, it doesn’t scale and can’t handle exceptions or retries as well as
specialized tools. Other ML-serving frameworks like Ray Serve, BentoML, and
vLLM may handle model serving better at scale by providing features such as
request batching. More sophisticated tools like Celery (a queue manager), Redis
(a caching database), and RabbitMQ (a message broker) can also be used in
combination to implement a more robust and reliable inference pipeline.

# Summary
![](https://i.imgur.com/ZAUgQLw.png)
