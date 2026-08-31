## Retrieval Augmented Generation (RAG)

A major realization was that the LLM should not be expected to remember enterprise knowledge.

Instead:
```java

Question

↓

Retrieve current enterprise knowledge

↓

LLM reasons over retrieved information
```
Enterprise documents include:

- Routing guides
- Configuration
- Architecture
- Business rules

Current knowledge Examples:
- Database
- Payload lookup
- Tracking
- OpenSearch