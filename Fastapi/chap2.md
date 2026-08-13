

# Dependency Injection System
in addition to helping structure your application logic, dependencies can help
you reduce duplication. They let you share and reuse logic across your API,
reuse open database connections, enforce security such as authentication or
authorization requirements, and much more.

In FastAPI, dependencies are also cached within the context of a single request
to prevent duplicate computations. This means a dependency function is
executed only once per request, and its result is reused for the duration of that
request if needed again. However, in a new request, the dependency function is
executed again.
Another great use case of the dependency injection system is
when you create a database connection and want to reuse that connection to
perform multiple fetch requests while processing a single request.

![](https://i.imgur.com/GpDhQ64.png)

As shown in Example 2-5, you can inject these dependencies into other
functions by passing them as parameters to Depends() for FastAPI to evaluate
and cache your function outputs
![](https://i.imgur.com/qQTWQwQ.png)


# Lifespan Events
FastAPI’s lifespan events are excellent for handling initialization and cleanup of
your service when you need to set up resources that can be shared between
requests. During server startup, you can create database connection pools or
load GenAI models into memory for reuse across requests. Afterward, before
server shutdown, you can clean up by unloading AI models, closing connection
pools, deleting temporary artifacts, and logging events.

By using lifespan events, your FastAPI service performs long-running operations
like model loading at the start, before serving requests, and keeps it loaded for
reuse among requests. During server shutdown, you can then gracefully finish all
remaining and queued requests before running any cleanup operations.


# REST endpoints
s an architectural style for
designing APIs where you use common HTTP methods like GET, POST, PUT,
and DELETE to access and manipulate resources.


# FastAPI Project Structures
There are a few project structures you can adopt: flat, nested, and modular.

### Flat Structure
The main idea here is to keep all similar code in modules and placed together
near the root of your project. For instance, put all your database models in
models.py or your endpoints in routes.py.

![](https://i.imgur.com/IWGH3aI.png)
If you are building a microservice with FastAPI, by definition, you will want to maintain a flat structure for simplicity.


### Nested Structure

The nested structure groups similar modules into packages—effectively creating
a nested structure and hierarchy of modules. You group all modules under a
package that are similar in nature irrespective of the feature they support. These
are loosely coupled modules that contain similar logic for different entities in
your project. For instance, the models package may contain users and
profiles database models.
![](https://i.imgur.com/aQeUfCe.png)

The main pitfall with this project structure is the ambiguous coupling of
modules. Changes in one module can cascade into other modules, and it can
become difficult to understand the cascading effect of new changes. Over time, it
can be challenging to maintain and change the code without performing many
updates everywhere else. This is referred to as **shotgun updates**. Shotgun updates in the context of software development are when it is challenging to maintain and change the code without performing many updates everywhere else


### Modular Structure
In the modular structure, modules that are closely related and refer to a specific
domain are grouped together. This approach differs from the previously
mentioned nested structure. An example could be the users package that
contains user schemas, database services, dependencies and routers.


![[260812_17h07m44s_screenshot 1.png]]


you bring
together closely interconnected components based on a feature or a global
system they implement (e.g., authentication, payment processing, notifications,
etc.) or the resource they interact with (e.g., users, profiles, messages, etc). This
kind of encapsulation eliminates any uncertainty regarding the couplings in your
code, resulting in improved scalability and maintainability.

If you need to include more features, you can create a new package that contains
all the necessary code. Similarly, if you need to modify or delete code, you can
easily determine where the changes should be made and expect how they will
impact other parts of the code. This is possible because the structure of the
codebase is transparent and well-encapsulated, making it clear where different
components are connected.

# Progressive Reorganization of Your FastAPI Project
When you first start your project, modularity is not as important. You can get
started with just a single or a few Python files to build your services easily.
However, as soon as you introduce AI models, external services, and complex
business logic, you will want to consider modularizing your codebase.

 you may be asking yourself, “Which project structure should I adopt for building generative AI services with FastAPI?”
 I found that the best way to structure projects is to progressively reorganize your
project from a flat to a modular structure as your service complexity grows:

1. Flat
If you are starting with a new project and the complexity of your system is
not yet clear, you can focus on writing all your FastAPI code in a single file
before worrying about the project structure. You then extract your code into
several files under the root directory. This is the initial structure you will
adopt when experimenting on the first version of your service from scratch.
2. Nested
As the number of files in your codebase and service complexity grows, you
can adopt the nested structure. You can search for files based on logical
grouping (models, routers, schemas, etc.) and do not have to worry too much
about logical couplings in your code. As you make changes, only a handful
of files are affected. At this point, you have an AI microservice.
3. Modular
As you move from a microservice to a full backend service, you will want to
adopt a modular structure. There is now an increasing number of modules,features, and complexity. You start grouping your code into packages based on areas of concern. Your code is now handling requests, authentication, external systems, etc., while serving an AI model.


# Onion/Layered Application Design Pattern
If you plan on building a fully featured backend service for generative AI, you
will benefit to know more about the onion, or layered, application design pattern,
which can be implemented within the nested and modular project structures. The
purpose of this pattern is to create a separation of concerns between the different
parts of your application to simplify the process of adding, removing, and
modifying features.

The onion design consists of layers, each with a specific responsibility and
dependency direction, shown in Figure 2-3.
![[260812_17h19m04s_screenshot.png]]
The innermost layer contains the domain models and business logic, while the outer layers contain route handling (in an API service) or user-interfacing code (when serving HTML templates).
The pattern is called “onion” because the layers build upon each other, with the
domain model at the center, surrounded by layers of increasing abstraction
promoting testability, maintainability, and flexibility in maintaining your AI
services. The core of the application (domain model and business logic) is at the
inner layers, and all other layers depend inwardly on it. This approach helps to
manage dependencies, promote separation of concerns, and facilitate a more
testable and maintainable codebase.

The main idea behind this pattern is the **dependency inversion principle**, which
states that high-level modules should not directly depend on the implementation
of low-level modules but declare what they need from low-level modules by
leveraging the FastAPI dependency system. The dependency system can then
inject the output of the low-level modules to avoid coupling between layers.


To implement this software design, you break down your service as an onion
consisting of layers that go deeper and deeper. Each layer (as you move from
outer to inner layers) introduces components that are responsible for a set of
tasks:
**API routers**
	Routers are responsible for grouping multiple controllers/route handlers to
apply common logic across several controllers.
	FastAPI provides the APIRouter class to help you with this.
**Controllers/route handlers**
	Controllers are responsible for handling incoming requests and returning
responses to the client via a logical execution of services or providers.
	Good controller design always uses dependencies to inject required data or
logic required for its execution

![[260812_17h23m37s_screenshot.png]]

**Services/providers**
	Services are responsible for combining or orchestrating multiple internal
operations to implement a business logic (services), while providers
implement the interface with external systems.
	Services typically use repositories for data access to implement complex
business logic rather than simple data retrieval and mutation operations.
Each module of your application can have its own service.

	Providers are similar to services but are specialized in interacting with external systems such as internal/third-party APIs. Examples of providers include clients for email servers, payment gateways, or other microservices.


	Essentially, both providers and services support implementation of controller business logic by facilitating internal and external interactions

	Here is an example of how they work together within a route controller: the users database service fetches a user’s record by email and then uses that information with a payment gateway and email server clients (providers) for processing payments and sending confirmation emails

**Repositories (data adapters)**
	A repository is a design pattern used when implementing the logic for data
access and mutation operations with data sources (not to be confused with a
Git repository).
	Repositories use object-relational mapping (ORM) or raw SQL commands to
execute queries on your infrastructure like a database, or a memory store for
retrieving or mutating data.
	You may implement an abstract interface in this layer to enforce consistent
design across all your repositories—using the create, read, update, delete
(CRUD) operations.

![[260812_17h27m17s_screenshot.png]]

**Schemas/models**
	These are responsible for enforcing type-safety, structure, and validation
logic on your data as it flows throughout your service


You will also have components that span layers to support the whole application:

**Middleware**
	This handles requests and responses before and after they are passed to the
application controllers/route handler
![[260812_17h29m09s_screenshot.png]]
**Dependencies**
	These include reusable functions you define that can be injected into
controllers to support a business logic. Dependencies can be cached and
depend on other dependencies.
**Pipes**
	These are data transformer functions that you can use across application
layers. Examples include data aggregators, cleaners, parsers, translators, etc.

**Mappers**
	These are data mappers from one schema into another, often passing data
across layers such as from the UserRequest schema at a router layer to the
UserInDB schema at the data access layer.
![[260812_17h30m37s_screenshot.png]]

**Exception filters**
	These consistently handle exceptions across the layers.
**Guards**
	These secure and protect controllers from abuse. Authentication and
authorization logic can be implemented as dependencies or middleware to
act as guards
![[260812_17h31m52s_screenshot 1.png]]

In the upcoming chapters, you’ll use these patterns to build the GenAI service
shown in Figure 2-9.

![[260812_17h33m21s_screenshot.png]]


# ASYNCHRONOUS SERVER GATEWAY INTERFACE
ASGI-based frameworks can process multiple requests by running
concurrent asynchronous operations on the main event loop, allowing it to
handle a higher volume of requests at scale.
It can also use a thread pool (i.e., a pool of thread workers) to perform
synchronous tasks concurrently without blocking the main server thread.
Once tasks are finished, these threads return control to the main web server
thread and share their results. When a thread raises an error, the web server
gathers information from the worker thread and sends an error response to
the client instead.
Modern web frameworks that implement the ASGI standard are not only
more efficient but also provide backward compatibility for WSGI in case it’s
necessary

# FastAPI Limits
#### Lack of Support for Micro-Batch Processing Inference Requests
Deep learning frameworks provide support for vectorization so that inferences
can be batched together, efficiently computed, and parallelized. Unfortunately,
prediction requests can’t be batched together in FastAPI, and as a result, each
compute-intensive model inference operation can block other requests.

When scaling services, a solution is to serve heavy models separately and use
FastAPI to authenticate and manage the incoming and outgoing data.

#### Cannot Efficiently Split AI Workloads Between CPU and GPU
While the CPU mostly handles request transformation and validation operations,
the GPU can run and parallelize compute-intensive model inference. In some
specialized ML web frameworks (like BentoML), you can also efficiently split
AI workloads between the CPU and GPU.
	**NOTE** : When you split AI workloads across the CPU and GPU, data preparation and post-processing operations run on the CPU, while faster deep learning inference is performed on the GPU
Unfortunately, FastAPI can’t efficiently perform this split of the AI inference
workload between these devices. This means your CPU can be blocked from
processing requests even when inference processes are running on the GPU. As
this is a big bottleneck when working with heavier models, it will require serving
heavier models outside FastAPI for concurrent workloads.

#### Dependency Conflicts
When you are deploying ML models, you will face unique challenges compared
to deploying typical web applications. This is due to your model runtime’s deep
coupling with native libraries and hardware. Each deployment environment can
operate on distinct hardware and may require you to use specific versions of
native libraries and containerization commands.

#### Lack of Support for Resource-Intensive AI Workloads
Despite its incredible capabilities, FastAPI was developed before the rise of
generative AI. As a result, it remains a general-purpose web framework with
recent support for AI serving and ML workflows. However, for certain use cases,
such as serving resource-intensive and complex billion-parameter models, it may
be worth exploring other frameworks like BentoML.

**BENTOML: FASTAPI-INSPIRED FRAMEWORK FOR
RUNNING RESOURCE-INTENSIVE AI MODELS** : BentoML is also built on top of Starlette and is designed with FastAPI
patterns in mind but specifically for machine learning. Its architecture allows
for scaling web requests separately from model inference, providing
flexibility in computing distributions.
It addresses unique ML workflow challenges using its Runners, dependency
management, and model versioning systems. Through its dependency
management system, it can effectively speed up deployments by
declaratively auto-generating Dockerfiles for you so that you do not have to
debug complex Docker commands to install and use CUDA libraries for
GPU inference. 
Later in the book, I will present a FastAPI architecture for resource-intensiv AI workflows that uses BentoML as the underlying AI server. In this
architecture, model-serving tasks will be delegated to BentoML, while
FastAPI will manage security, caching, and business logic for you.