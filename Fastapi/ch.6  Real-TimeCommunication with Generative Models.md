
**CHAPTER GOALS**
In this chapter, you will learn about:
- When to implement real-time communication in AI workflows
Web communication mechanisms by comparing their features,
differences, and similarities
- Real-time communication mechanisms including server-sent events
(SSE) and WebSocket (WS)
- Selecting the correct streaming technology for your use case
Implementing mocked streaming endpoints from scratch for testing
and prototyping
- Implementing real-time API endpoints with both SSE and
WebSocket mechanisms
- How to gracefully handle exceptions and close streaming
connections
- API design patterns to simplify streaming endpoints


# Web Communication Mechanisms
Concurrency solves the problem of allowing simultaneous users
to access your service and helps to decrease the waiting times, yet AI data generation remains a resource-intensive and time-consuming task.

Up until this point, you’ve been building endpoints using the conventional HTTP
communication where the client sends a request to the server. The web server
processes the incoming requests and responds via HTTP messages.
![](https://i.imgur.com/cwzB25e.png)

Since the HTTP protocol is stateless, the server treats each incoming request
completely independent and unrelated from other requests. This means that
multiple incoming requests from differing clients wouldn’t affect how the server
 responds to each one. As an example, in a conversational AI service that doesn’t
use a database, each request may provide the full conversation history and
receive the correct response from the server.
The HTTP request-response model is a widely adopted API design pattern used
across the web due to its simplicity. However, this approach becomes inadequate
as soon as the client or the server needs real-time updates.

In the standard HTTP request-response model, your services typically respond to
the user’s request once it has been entirely processed. However, if the data
generation process is lengthy and sluggish, your users will wait a long time and
subsequently be inundated with lots of information at once. Imagine chatting to a
bot that takes several minutes to reply, and once it does, you’re shown
overwhelming blocks of text.
Alternatively, if you provide the data to the client as it’s being generated, rather
than holding off until the entire generation process is complete, you can mitigate
lengthy delays and deliver the information in digestible chunks. This approach
not only enhances user experience but also maintains user engagement during
the ongoing processing of their request.

There will be cases where implementing real-time features can be overkill and
escalate the development burden. For instance, some open source models or
APIs lack the real-time generation capability. Furthermore, adding data
streaming endpoints can add to the complexity of your system on both sides, the
server and the client. It means having to handle exceptions differently and
manage concurrent connections to the streaming endpoints to avoid memory
leakage. If the client disconnects during a stream, there may be a chance for data
loss or state drift between the server and the client. And, you may need to
implement complex reconnection and state management logic to handle cases
where the connection drops


Equally important, you also need to consider the scalability of handling a large
number of concurrent streams, your application’s latency requirements, and
browser compatibilities with your chosen streaming protocol.


If your use case does benefit from real-time features, then you have a few
architectural design patterns you can implement:
- Regular/short polling
- Long polling
- SSE
- WS


#### Regular/Short Polling
![](https://i.imgur.com/iagN7QW.png)
A method to benefit from semi-real-time updates is to use regular/short polling,
as shown in Figure 6-2. In this polling mechanism, the client periodically sends
HTTP requests to the server to check for updates at preconfigured intervals. The
shorter the intervals, the closer you get to real-time updates but also the higher
the traffic you will have to manage.

You can use this technique if you’re building a service to generate data such as
images in batches. The client simply submits a request to start the batch job and
is given a unique job/request identifier. It then periodically checks back with the
server to confirm the status and outputs of the requested job. The server then
responds with new data or provides an empty response (and perhaps a status
update) if outputs are yet to be computed

As you can imagine with short polling, you’ll end up with an excessive number
of incoming requests that the server needs to respond to, even when there’s no
new information. If you have multiple concurrent users, this approach can
quickly overwhelm the server, which limits your application’s scalability.
However, you can still reduce server load by using cached responses (i.e.,
executing status checks on the backend at a tolerable frequency) and
implementing rate limiting, which you will learn more about in Chapters 9 and
10.



<mark>A potential use case for short polling in AI services is when you have some in-
progress batch or inference jobs. You can expose endpoints for your clients to
use short polling to keep up-to-date with the status of these jobs. And, fetch the
results when they’re completed.</mark>

#### Long Polling
If you want to reduce the burden on your server while continuing to leverage a
real-time polling mechanism, you can implement long polling (see Figure 6-3),
an improved version of regular/short polling.
![](https://i.imgur.com/qYbsnaI.png)
With long polling, both the server and the client are configured to prevent
timeouts (if possible) that occur when either the client or the server gives up on
the prolonged request.

To implement long polling, the server keeps the incoming requests open (i.e.
hanging) until there is data available to send back. For instance, this can be
useful when you have an LLM with unpredictable processing times. The client is
instructed to wait for an extended period of time and avoid aborting and
repeating the requests prematurely.

You can use long polling if you need a simple API design and application
architecture for processing prolonged jobs, such as multiple AI inferences. This
technique allows you to avoid implementing a batch job manager to keep track
of jobs for bulk data generation. Instead, the client requests remain open until
they are processed, avoiding the constant short polling request-response cycle
that can overload the server.
While long polling sounds similar to the typical HTTP request-response model,
it differs on how the client handles requests. In long polling, the client typically
receives a single message per request. Once the server sends a response, the
connection is closed. The client then immediately opens a new connection to
wait for the next message. This process repeats, allowing the client to receive
multiple messages over time, but each HTTP request-response cycle handles
only one message.

Since long polling maintains an open connection until a message is available, it
reduces the frequency of requests compared to short polling and implements a
near-real-time communication mechanism. However, the server still has to hold
onto unfulfilled requests, which consume server resources. Additionally, if there
are multiple open requests by the same client, message ordering can be
challenging to manage, potentially leading to out-of-order messages.

If you don’t have a specific requirement for using polling mechanisms, a more
modern alternative to polling mechanisms for real-time communication is SSE
via the Event Source interface

#### Server-Sent Events
Server-sent events (SSE) is an HTTP-based mechanism for establishing a
persistent and unidirectional connection from the server to the client. While the
connection is open, the server can continuously push updates to the client as data becomes available.
Once the client establishes the persistent SSE connection with the server, itwon’t need to re-establish it again, unlike the long polling mechanism where the
client repeatedly sends requests to the server to maintain an open connection.
When you’re serving GenAI models, SSE will be a more suitable real-time
communication mechanism compared to long polling. SSE is designed
specifically for handling real-time events and is more efficient than long polling.
Due to repeated opening and closing connections, long polling becomes resource
intensive and leads to higher latency and overhead. SSE, on the other hand,
supports automatic reconnection and event IDs to resume interrupted streams,
which long polling lacks.

In SSE, the client makes a standard HTTP GET request with an
Accept:text/event-stream header, and the server responds with a status code
of 200 and a Content-Type: text/event-stream header. After this
handshake, the server can send events to the client over the same connection.
![](https://i.imgur.com/lo7Yev7.png)

While SSE should be your first choice for real-time applications, you can still
opt for a simpler long polling mechanism where updates are infrequent or if your
environment doesn’t support persistent connections.

One last important detail to note is that SSE connections are unidirectional,
meaning that you send a regular HTTP request to the server, and you get the
response via SSE. Therefore, they’re only suitable for applications that don’t
need to send data to the server. You may have seen SSE in action within news
feeds, notifications, and real-time dashboards like stock data charts.

Unsurprisingly, SSE also shines in chat applications when you need to stream
LLM responses in a conversation. In this instance, the client can establish a
separate persistent connection until the server fully streams the LLM’s response
to the user
> [!note] ChatGPT leverages SSE under the hood to enable real-time responses to user queries.

![](https://i.imgur.com/vRDdGUb.png)

In summary, SSE is excellent for establishing persistent unidirectional
connections, but what if you need to both send and receive messages during a
persistent connection? This is where WebSocket would come in handy.

#### WebSocket
WebSocket is an excellent real-time communication mechanism for establishing
persistent bidirectional connections between the client and the server for real-
time chat, as well as voice and video applications with an AI model. 
 Web applications that require two-way communication with servers benefit the most from this mechanism as they can avoid the overhead and complexity of HTTP polling.
 ![](https://i.imgur.com/TIxzf5O.png)

Unlike all other communication mechanisms discussed so far, the WebSocket
protocol doesn’t transfer data over HTTP after the initial handshake. Instead, the
WebSocket protocol defined in the RFC 6455 specification implements a two-
way messaging mechanism (full-duplex) over a single TCP connection.
 As a result, WebSocket is faster for data transmission than HTTP because it has less protocol overhead and operates at a lower level in the network protocol stack.
This is because HTTP sits on top of TCP, so stripping back to TCP will be faster.
> [!tip] WebSocket keeps a socket open on both the client and the server for the duration of the connection. Note that this also makes servers stateful, which makes scaling trickier.

According to RFC 6455, to establish a WebSocket connection, the client sends
an HTTP “upgrade” request to the server, asking to open a WebSocket
connection. This is referred to as the opening handshake, which initiates the
WebSocket connection lifecycle in the CONNECTING state.
> [!warning] Your AI services should be able to handle multiple concurrent handshakes and also authenticate them before opening a connection. New connections can consume server resources, so they must be handled properly by your server

The HTTP upgrade request should contain a set of required headers
![](https://i.imgur.com/caPqF29.png)
Once the WebSocket connection is established, text or binary messages can be
transmitted in both directions in the form of message frames. The connection
lifecycle is now in the OPEN state.
![](https://i.imgur.com/v5PWYXk.png)

Message frames are a way to package and transmit data between the client and
server. They aren’t anything unique to WebSocket as they apply to all
connections over the TCP protocol that form the basis of HTTP. However, a
WebSocket message frame consists of several components:
**Fixed header**
	Describes basic information about the message
**Extended payload length (optional)**
	Provides the actual length of the payload when the length exceeds 125 bytes
**Masking key**
	Masks the payload data in frames sent from the client to the server,
preventing certain types of security vulnerabilities, particularly cache
poisoning1 and cross-protocol2 attacks
**Payload**
	Contains the actual message content
Unlike the verbose headers in HTTP requests, WebSocket frames have minimal
headers that include the following:
**Text frames**
	Used for UTF-8 encoded text data
**Binary frames**
	Used for binary data
**Fragmentation**
	Used to fragment messages into multiple frames, which are reassembled by
the recipient

The beauty of the WebSocket protocol is also its ability to maintain a persistent
connection through control frames.
Control frames are special frames used to manage the connection:
**Ping/pong frames**
	Used to check the connection’s status
**Close frame**
	Used to terminate the connection gracefully
The CLOSING state ends once the other party responds with another close
frame. This concludes the full WebSocket connection lifecycle at the CLOSED
state, as shown in Figure 6-6.
![](https://i.imgur.com/qG66KJh.png)

As you can see, using the WebSocket communication mechanism can be a bit of
overkill for simple applications that won’t require the overheads. For most
GenAI applications, SSE connections may be enough.


## Comparing Communication Mechanisms

![](https://i.imgur.com/n1VsYIm.png)


HTTP request-response is the most common model supported by all web clients
and servers, suitable for RESTful APIs and services that don’t require real-time
updates.
Short/regular polling involves clients checking for data at set intervals, which is
straightforward but can be resource-intensive when scaling services. It is
normally used in applications to perform infrequent updates such as in analytics
dashboards.
Long polling is more efficient for real-time updates by keeping connections open
until data is available on the server. However, it can still drain the server
resources, making it ideal for near-real-time features such as notifications.

SSE maintains a single persistent connection that is server-to-client only, using
the HTTP protocol. It is straightforward to set up, leverages the browser’s
EventSource API and ships with built-in features like reconnection. These
factors make SSE suitable for applications requiring live feeds, chat features,
and real-time dashboards.

WebSocket provides full-duplex (double-sided) communication with low latency
and binary data support, but is complex to implement. It is widely used in
applications requiring high interactivity and real-time data exchange, such as
multiplayer games, chat applications, collaborative tools, and real-time
transcription services.
![](https://i.imgur.com/4ajFgYM.png)
![](https://i.imgur.com/4eXdsyo.png)

# Implementing SSE Endpoints
Model providers will normally expose an option for you to set the output mode
as a data stream using stream=True. With this option set, the model provider
can return a data generator instead of the final output to you, which you can
directly pass to your FastAPI server for streaming.
![](https://i.imgur.com/sNRX8dY.png)
![](https://i.imgur.com/zPJfb0p.png)
By setting the stream=True, AsyncAzureOpenAI returns a data stream (an async
generator function) instead of the full model response. You can loop over the
data stream and yield tokens with the data: prefix to comply with the SSE
specification. This will let browsers to automatically parse the stream content
using the widely available EventSource web API.

> [!warning]
> When exposing streaming endpoints, you’ll need to consider how fast the clients can consume
the data you’re sending them. A good practice is to reduce the streaming rate as you saw in
Example 6-2 to reduce the back pressure on clients. You can adjust the throttling by testing
your services with different clients on various devices.
#### SSE with GET Request
You can now implement the SSE endpoint by passing the chat stream to the
FastAPI’s StreamingResponse as a GET endpoint, as shown in Example 6-3.
![](https://i.imgur.com/y5yn0uB.png)
With the GET endpoint set up on the server, you can create a simple HTML form
on the client to consume the SSE stream via the EventSource interface, as
shown in Example 6-4.
![](https://i.imgur.com/pX8v10L.png)
![](https://i.imgur.com/FA0BFQA.png)
![](https://i.imgur.com/S5X47Tk.png)
With the SSE client implemented in Example 6-4, you can now use it to test your
SSE endpoint. However, you need to serve the HTML first.
Create a pages directory and then place the HTML file inside. Then mount the
directory onto your FastAPI server to serve its content as static files, as shown in
Example 6-5. Via mounting, FastAPI takes care of mapping API paths to each
file so that you can access them with a browser from the same origin as your
server
![](https://i.imgur.com/9Xtqxkp.png)
By implementing Example 6-5, you serve the HTML from the same origin as
your API server. This avoids triggering the browser’s CORS security
mechanism, which can block outgoing requests reaching your server.


**Cross-origin resource sharing**

CORS is a security mechanism implemented in browsers to control how
resources on a web page can be requested from another domain, and is relevant
only when sending requests directly from the browser instead of a server.
Browsers use CORS to check whether they’re allowed to send requests to the
server from a different origin (i.e., domain) than the server.

For example, if your client is hosted on https://example.com and it needs to
fetch data from an API hosted on https://api.example.com, the browser will
block this request unless the API server has CORS enabled.
![](https://i.imgur.com/1GANERa.png)
`1.Allow incoming requests from any origins, methods (GET, POST, etc.) and headers.`
![](https://i.imgur.com/q7VPlMy.png)


#### Streaming LLM outputs from Hugging Face models
Although Hugging Face’s transformers library implements a TextStreamer
component that you can pass to your model pipeline, the easiest solution is to
run a separate inference server such as HF Inference Server to implement model
streaming.
