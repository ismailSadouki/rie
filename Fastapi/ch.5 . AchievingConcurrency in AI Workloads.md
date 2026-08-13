
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














---


what is the difference between a thread and a core

what is concurrency and parallelism in python
multithreading, asynchronous programming, multiprocessing, workloads

