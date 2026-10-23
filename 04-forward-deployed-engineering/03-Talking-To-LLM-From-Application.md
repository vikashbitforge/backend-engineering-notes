# Talking to an LLM from Your Application

## 1. Introduction

An LLM and a Generative AI application are not the same thing.

An LLM provides language-generation capabilities. A GenAI application is software built around those capabilities to solve a particular problem.

For example, a support-ticket summarization application may use an LLM to generate summaries, but the surrounding application is responsible for receiving the ticket, preparing the request, communicating with the model provider, and returning the result.

A useful mental model is:

```text
User
  ↓
Application
  ↓
LLM Provider API
  ↓
Language Model
  ↓
Generated Response
  ↓
Application
  ↓
User
```

The LLM provides the language intelligence. The application controls how that capability is used.

## 2. Why a Raw LLM Is Not a Complete Application

Consider this question:

> What is the weather in Delhi right now?

A model cannot automatically retrieve live weather data just because the user asks for it. It can only use information available in its context and knowledge acquired during training.

An application can connect the model to an external weather API:

```text
User asks about current weather
              ↓
         Application
              ↓
       Weather API call
              ↓
       Current weather data
              ↓
             LLM
              ↓
    Natural-language answer
```

The model generates the explanation, while the application retrieves current information.

The same principle applies to databases, calculators, search engines, and business APIs.

## 3. The Application Controls Information Flow

One of the most important principles in GenAI application engineering is:

**The LLM can only use the information and capabilities made available to it.**

If a task requires information from a database, the application must retrieve the relevant data and provide it to the model.

If a task requires a reliable calculation, the application can call a calculator or execute code.

If a task requires an action, such as updating an order, the application must invoke the appropriate business operation and handle its result.

The model can help interpret natural language and determine what information or action may be needed. The application connects that interpretation to real systems.

## 4. Calling an LLM Through an API

An application can communicate directly with an LLM provider without using a consumer-facing application such as ChatGPT.

At a high level, this is ordinary client-server communication:

```text
Our Application
      |
      | HTTPS Request
      v
  LLM Provider
      |
      | HTTP Response
      v
Our Application
```

An API request generally needs four important pieces of information:

| Component | Purpose |
|---|---|
| Endpoint | Where the request should go |
| API credential | Authenticates the application |
| Model | Specifies which model to use |
| Input | Supplies the task or content to process |

The response can contain generated content as well as metadata, such as the response identifier, model information, status, and token usage.

API credentials should be kept private, stored securely, and never committed to a public repository.

## 5. Token Usage Is an Engineering Concern

Tokens are not just an internal detail of language models. They affect practical application decisions.

- **Cost:** Token usage can influence the cost of model requests.
- **Latency:** The amount of input and output processed can affect response time.
- **Context size:** The model has limits on how much information it can process in a request.

Input tokens represent the content supplied to the model. Output tokens represent the content it generates.

Monitoring usage helps engineers understand how an application behaves and what it costs to operate.

## 6. Integrating an LLM into a Java Application

For a Java backend, Spring AI provides abstractions for interacting with supported model providers.

The provider supplies the underlying model. Spring AI helps the Java application communicate with it.

A simplified architecture looks like this:

```text
Client
  |
  | POST /api/summarize
  v
Spring Boot Controller
  |
  v
Summarizer Service
  |
  v
Spring AI ChatClient
  |
  v
LLM Provider API
  |
  v
Language Model
```

The response travels back through the application to the client.

This separates the responsibilities of the application:

- **Controller:** Receives the HTTP request.
- **Service:** Implements the summarization workflow.
- **ChatClient:** Prepares and sends the model interaction.
- **LLM provider:** Processes the request and generates a response.

### Example service

The following illustrates the core idea using Spring AI's `ChatClient` API:

```java
@Service
public class SummarizerService {

    private final ChatClient chatClient;

    public SummarizerService(ChatClient.Builder builder) {
        this.chatClient = builder.build();
    }

    public String summarize(String ticket) {
        return chatClient.prompt()
                .user("""
                    Summarize this support ticket in two sentences:

                    %s
                    """.formatted(ticket))
                .call()
                .content();
    }
}
```

This example demonstrates the basic interaction. A production service would also need suitable validation, error handling, observability, and safeguards for the data it processes.

## 7. Why Use an Abstraction Such as Spring AI?

An application can call a provider's HTTP API directly. A framework abstraction is not strictly required.

However, as the application grows, it may need conversation memory, streaming, structured outputs, tool calling, retrieval-augmented generation (RAG), multiple model providers, and observability.

An abstraction can help organize these interactions and reduce provider-specific code in application logic. Its value depends on the project's requirements; a small integration may not need much abstraction.

## 8. Separate Application Instructions from User Data

Consider a summarization request constructed like this:

```text
Summarize this support ticket in two sentences:

Payment was deducted, but my order is still processing.
```

This message combines two different things:

1. An application instruction that describes the task.
2. User-provided data that needs to be processed.

These have different purposes. Keeping that distinction clear is an important design consideration when building model interactions. The way instructions and data are represented can affect how reliably the application behaves.

## 9. Key Takeaways

1. An LLM is a capability, not a complete application.
2. The surrounding software determines what information reaches the model and what happens with its response.
3. External APIs and tools can provide current information or perform deterministic operations.
4. Calling an LLM provider is fundamentally an API interaction.
5. API credentials must be protected.
6. Token usage affects cost, latency, and context limits.
7. Spring AI provides abstractions for integrating models into Java applications.
8. Production integrations need more than a successful model call: they need appropriate validation, error handling, and operational visibility.

## Final Mental Model

```text
Business Requirement
        ↓
Application Workflow
        ↓
Prepare Context and Instructions
        ↓
Call LLM Provider
        ↓
Receive Model Response
        ↓
Validate and Process Result
        ↓
Return Result or Continue Workflow
```

**The key engineering lesson:** The LLM provides a capability; the application turns that capability into a useful software feature.
