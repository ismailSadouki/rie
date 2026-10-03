![](https://i.imgur.com/wBCFpGK.png)



  One interesting benchmark I came across is **OpenRouter's Tool Call Error Rate benchmark**.

What caught my attention is that all these providers are serving the **same model, GLM 5.2**, yet the reported error rates vary significantly, from around **0.2% to 16.7%** (October 2).

So why can the same model behave so differently?

I think several layers of the inference stack could explain this:

**1. Inference and quantization**

Different providers may use different inference engines, configurations, model revisions, and quantization schemes.

For example, serving a model in **FP4 instead of FP8** can affect its output distribution and potentially its tool-calling reliability.

Another technique is **speculative decoding**, where a smaller draft model proposes tokens that are verified by the larger model to accelerate inference.

**2. Constrained decoding**

Tool calling requires structured outputs, such as valid JSON with the expected arguments.

Some inference engines, such as **vLLM**, support grammar-based and structured-output decoding, which can help enforce valid formats. Other implementations may rely more heavily on the model to generate correctly structured tool calls.

**3. Tool-call parsing and API implementation**

Providers may differ in how they serialize tool definitions, parse arguments, validate JSON, and translate between internal formats and OpenAI-compatible APIs.

These differences can affect whether a generated tool call is successfully interpreted.

**4. Infrastructure**

Timeouts, rate limits, overloaded servers, and other provider-side failures may also contribute, depending on how the benchmark defines an error.

This raises an important question: when we talk about a model's tool-call error rate, are we really measuring the model itself, or the **entire inference stack**?

For agentic AI, I think this distinction matters. The model is only one component; **how it is served can be just as important in practice.**

A controlled benchmark that isolates quantization, decoding strategies, tool-call parsing, and infrastructure would help us better understand these differences.

#LLM #AgenticAI #ToolCalling #LLMInference #Inference #AIEngineering #MachineLearning 


