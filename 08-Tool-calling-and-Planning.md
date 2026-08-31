# Chapter 8 - Tool Calling: Designing Enterprise Capabilities

> *"The LLM should think in business capabilities. The application should think in implementation details."*

---

# Introduction

One of the biggest differences between consumer AI applications and Enterprise AI systems is the ability for an AI model to interact with enterprise capabilities.

A large language model (LLM) has no inherent knowledge of an organization's databases, APIs, message queues, or business rules. It only knows what is provided in the current conversation.

To answer real business questions, the application must provide controlled access to enterprise capabilities. This concept is known as **Tool Calling**.

Tool Calling is not about allowing an LLM to execute arbitrary code. It is about allowing the LLM to request specific business capabilities that are implemented and controlled by the application.

---

# The Problem

Consider the following question:

> **"Why did shipment 123456 fail rate shopping?"**

Without additional information, the LLM knows nothing about:

- Shipment 123456
- Transportation Management Systems
- Warehouse Management Systems
- Enterprise databases
- Application logs
- Routing rules

The LLM cannot answer this question accurately.

The application must first gather evidence before the LLM can reason over it.

```text
Question
    │
    ▼
Enterprise Capabilities
    │
    ▼
Evidence
    │
    ▼
LLM
    │
    ▼
Explanation
```

---

# What Is a Tool?

A common misconception is that a tool is simply an API or a database query.

From an Enterprise AI perspective, a tool should be viewed differently.

> **A Tool is a business capability that the application allows the LLM to request.**

Examples of good tools include:

- getShipmentEvidence()
- getCarrierDecision()
- getTrackingHistory()
- getProcessingStatus()
- searchKnowledgeBase()

Notice that none of these expose implementation details.

The LLM asks **what** it needs.

The application decides **how** to obtain it.

---

# Good vs. Bad Tool Design

## Poor Tool Design

```text
executeSql()
queryOracle()
callRestApi()
readKafkaTopic()
```

These tools expose implementation details.

They tightly couple the LLM to the application's internal architecture.

---

## Better Tool Design

```text
getShipmentEvidence()
getCarrierDecision()
getTrackingHistory()
searchKnowledgeBase()
```

These tools describe business capabilities rather than technical implementation.

The underlying implementation can change without affecting the LLM.

---

# Separation of Responsibilities

Tool Calling does not reduce the role of the application.

It actually makes the application more important.

## Responsibilities of the Application

- Authentication
- Authorization
- Validation
- Retry logic
- Caching
- Logging
- Correlation IDs
- Data normalization
- Calling enterprise APIs
- Querying databases

## Responsibilities of the LLM

- Understanding the user's question
- Requesting appropriate capabilities
- Reasoning over evidence
- Explaining results
- Producing structured responses

The application owns deterministic logic.

The LLM owns reasoning.

---

# Who Chooses the Tool?

There are several architectural approaches.

## Option 1 – Java Chooses

```text
User
    │
    ▼
Application
    │
    ▼
Tool
    │
    ▼
LLM
```

Advantages:

- Deterministic
- Easy to secure
- Easy to audit

Disadvantages:

- Every new scenario requires application changes.

---

## Option 2 – LLM Chooses

```text
User
    │
    ▼
LLM
    │
    ▼
Tool
    │
    ▼
LLM
```

Advantages:

- Flexible
- Natural language
- Easy to extend

Disadvantages:

- More difficult to control
- May call unnecessary tools
- Depends heavily on good tool descriptions

---

## Option 3 – Hybrid (Recommended)

```text
User
    │
    ▼
Planner
    │
    ▼
Allowed Tool Set
    │
    ▼
LLM
    │
    ▼
Tool Execution
    │
    ▼
LLM
```

The application limits the available tools.

The LLM selects from only the permitted capabilities.

This approach combines security with flexibility.

---

# The Planner

The Planner is responsible for orchestrating an investigation.

It does not perform reasoning.

Instead, it decides which capabilities should be available for a particular request.

For example:

Question:

> "Why wasn't FedEx selected?"

The planner may determine that only the following capabilities are relevant:

```text
getShipmentEvidence()
getCarrierDecision()
getProcessingStatus()
searchKnowledgeBase()
```

The LLM cannot request capabilities that are outside this set.

---

# Security Is Not a Tool

One of the most important architectural lessons is that authentication and authorization should never be exposed as AI tools.

Instead, security is enforced before the LLM begins reasoning.

```text
User
    │
    ▼
Authentication
    │
    ▼
Authorization
    │
    ▼
Planner
    │
    ▼
Allowed Tools
    │
    ▼
LLM
```

The application determines what the AI is permitted to access.

The AI should never determine what it is allowed to access.

---

# Managing Complexity

As systems grow, the number of available tools also increases.

A common mistake is creating a large matrix mapping every:

- Intent
- Tool
- Permission
- User Role

While this provides fine-grained control, it quickly becomes difficult to maintain.

Instead, consider grouping tools into logical capability categories.

For example:

- Shipment
- Carrier
- Tracking
- Operations
- Configuration
- Knowledge

Permissions can then be applied to categorize rather than every individual tool.

The planner narrows the available capability groups before exposing tools to the LLM.

This approach scales much better as the application evolves.

---
# Tool Metadata 

A tool consists of more than an implementation.

Every tool also includes metadata that helps the LLM understand:

- What the capability does
- What information it returns
- When it should be used

Unlike Java developers, an LLM has no knowledge of the application's architecture.

It relies entirely on the metadata provided by the application.

Well-designed tool descriptions improve tool selection without requiring changes to the implementation.

## Poor Description

Returns shipment information.

### versus

## Better Description

Retrieves shipment evidence, including processing events, tracking history, retries, and errors. Use this tool when investigating shipment processing, routing, or carrier-selection questions.

---

> **Architect's Insight**
>
> Tool descriptions are not documentation for developers.
> They are instructions for the LLM.
>
> A good tool description explains:
>
> - What the capability does
> - What information it returns
> - When it should be used
>
> The LLM cannot infer intent from implementation.

---

# Design Guidelines

When designing enterprise AI tools:

- Think in business capabilities, not infrastructure.
- Hide implementation details from the LLM.
- Prefer fewer, richer tools over many small tools.
- Keep deterministic logic inside the application.
- Allow the LLM to reason, not to enforce business rules.
- Expose only the capabilities required for the current investigation.
- Treat security as a responsibility of the application.

---

# Common Mistakes

Avoid designing tools that expose implementation details.

```text
executeSql()
queryDatabase()
callRestApi()
```

Avoid allowing the LLM unrestricted access to enterprise systems.

Avoid embedding business logic inside prompts.

Avoid asking the LLM to determine facts that the application can determine deterministically.

---

# Interview Questions

### Recall

1. What is a Tool in an Enterprise AI system?
2. Why should tool names represent business capabilities rather than implementation details?
3. What responsibilities belong to the application instead of the LLM?

---

### Design

1. Design a Tool Calling strategy for a shipment investigation assistant.
2. How would you reduce complexity if your application contained 50 tools?
3. When should the application decide which tools are available instead of the LLM?

---

### Architecture

1. Why is `executeSql()` a poor enterprise AI tool?
2. Explain the trade-offs between Java-selected tools and LLM-selected tools.
3. Why should authentication and authorization occur before Tool Calling?
4. How would you design Tool Calling so the application could switch AI providers without changing business logic?

---

# Evolution of My Thinking

## Before

A tool is simply an API or a database query.

## After

A tool is a business capability that the application allows the LLM to request.

---

## Before

The LLM should access whatever information it needs.

## After

The application determines what information the LLM is permitted to access.

---

## Before

Authentication can be another tool.

## After

Authentication and authorization define the boundaries within which the AI operates.

---

# Key Takeaways

- Tool Calling enables AI to interact with enterprise capabilities safely.
- Tools should represent business capabilities rather than technical implementation.
- The application owns security, validation, and deterministic logic.
- The LLM owns reasoning and explanation.
- A planner can improve both security and efficiency by limiting the tools available to the LLM.
- Enterprise AI systems should expose capabilities, not infrastructure.