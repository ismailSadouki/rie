
- When a database is necessary and how to identify the appropriate
database type for your project
- The underlying mechanism of relational databases and the use cases
of nonrelational databases
- The development workflow, tooling, and best practices for working
with relational databases
- The techniques to improve query performance and efficiency when
working with databases
- How to manage ever-evolving database schema changes
Strategies for managing the codebase, the database schema, and
data drifts when working in teams


# The Role of a Database
![](https://i.imgur.com/Uya7LJa.png)


To help you with creating a mental model of both relational and nonrelational
databases, take a look at Figure 7-2
![](https://i.imgur.com/Gml7L0J.png)
![](https://i.imgur.com/hGwyFVf.png)
![](https://i.imgur.com/tTvHF83.png)

Imagine you’re building a RAG-enabled LLM service that can talk to a
knowledge base. The documents in this knowledge base are related to each other, so you decide to implement a RAG graph to capture a richer context. To implement a RAG graph, you integrate your service with a graph-based database.

Now, to retrieve relevant chunks of documents, you also need to embed them in
a vector database. As part of this, you also need a relational database to monitor
usage, and store user data and conversation histories.

nce the users may ask common questions, you also decide to cache the LLM
responses by generating several outputs in advance. Therefore, you also integrate a key-value store to your service


Finally, you want to give administrators control over system prompts with the
ability to version-control prompts. So, you add a content management system as
a prompt manager to your solution. However, since the prompt templates can
often change, you also decide to integrate a document database.
![](https://i.imgur.com/XIy4jfm.png)


# Project: Storing User Conversations with an LLM in a Relational Database

