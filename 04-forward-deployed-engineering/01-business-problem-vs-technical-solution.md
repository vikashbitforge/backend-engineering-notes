# From Business Problem to a Real Engineering Solution

## What I learned

I started learning Forward Deployed Engineering (FDE) and Generative AI.

One of the first ideas that stood out to me was that a customer's proposed solution is not necessarily the actual requirement.

For example:

> "We want an AI chatbot."

At first, this sounds like a technical requirement.

But the actual business problem might be:

* Customers are waiting too long for responses.
* Support executives are repeatedly answering the same questions.
* Different executives are giving inconsistent answers.
* Support costs are increasing.
* Important cases are getting buried.
* Support is unavailable outside working hours.

A better requirement might therefore be:

> Reduce the time required to resolve customer problems without reducing accuracy or customer trust.

The chatbot is only one possible solution.

---

## Request vs Requirement

A useful distinction I learned:

```text
Customer Request
       ↓
Understand Business Problem
       ↓
Understand Existing Workflow
       ↓
Identify Constraints
       ↓
Design Technical Solution
       ↓
Integrate With Existing Systems
       ↓
Test & Evaluate
       ↓
Production
```

The FDE's job is not simply to implement whatever technical solution was requested.

The engineer needs to understand the problem first and determine what solution actually fits the environment.

---

## Example: Customer Support

Suppose an e-commerce company receives thousands of support requests every day.

Customers might ask:

* Where is my order?
* Can I return this product?
* Why was my payment deducted?
* Can I cancel my order?
* My package says delivered, but I didn't receive it.

A simple requirement might be:

> Build an AI chatbot.

But a real production system may need to do much more.

```text
Customer
   ↓
Application
   ↓
LLM
 ↙ ↓ ↘
Company   Customer   Business
Knowledge   Data       APIs
   ↓
Validation
   ↓
Security
   ↓
Action / Response
   ↓
Human Oversight when required
```

The LLM may help understand the customer's language, but the surrounding application is responsible for connecting that understanding to real business systems.

---

## Generating Text vs Performing an Action

Suppose the customer says:

> "Cancel my order and refund my payment."

An LLM can generate:

> "Your order has been cancelled."

But generating that sentence does not actually cancel the order.

A real system needs something closer to:

```text
Customer Request
       ↓
LLM understands the requested action
       ↓
Application validates the request
       ↓
Cancellation API
       ↓
Database / Order System
       ↓
Confirmed Result
       ↓
Response to Customer
```

This creates real engineering questions:

* Is the user authorized?
* Has the order already shipped?
* Is the order cancellable?
* What happens if the API fails?
* What if the model extracts the wrong order ID?
* Should large refunds require human approval?
* How do we audit the operation?
* How do we test the system?
* What latency is acceptable?
* What does each interaction cost?

This is where the problem becomes much larger than "build a chatbot."

---

## An LLM Is Not the Entire Application

Another important lesson from this session:

> An LLM is one component of a production AI system, not the complete system.

A real application may need:

* Company knowledge
* Customer data
* Existing APIs
* Business rules
* Validation
* Security
* Evaluation
* Observability
* Human oversight

Conceptually:

```text
Customer
   ↓
Application
   ↓
LLM
 ↕
Company Knowledge
 ↕
Customer Data
 ↕
Business APIs
 ↕
Validation Rules
 ↕
Security
 ↕
Human Oversight
```

The goal is not simply to make the model generate a convincing answer.

The goal is to build a system that is useful, grounded, secure, reliable, testable, and connected to real systems.

---

## Key Takeaway

The biggest lesson from my first FDE session is:

> **Don't start with the technology. Start with the problem.**

"Build an AI chatbot" is a technology-oriented request.

"Reduce customer-support resolution time without reducing accuracy or trust" describes a business problem.

The engineering work begins by understanding the difference.

---

## Connection to My Engineering Journey

I'm learning FDE alongside System Design and Low-Level Design.

So far, I see the connection as:

```text
Low-Level Design
       ↓
System Design
       ↓
Distributed Systems
       ↓
Cloud
       ↓
Forward Deployed Engineering
       ↓
Generative AI Applications
```

My goal is not just to learn how to call an LLM API.

I want to understand how to turn a real-world problem into a reliable engineering system.

This is the beginning of that journey.
