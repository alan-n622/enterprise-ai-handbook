## Spring AI

Learned how Spring AI allows the application to remain independent of the underlying AI provider.

Instead of coupling business logic directly to Bedrock or OpenAI, the application communicates through abstractions such as:

- AiClient
- ChatClient
- ChatClientConfig

This makes future model changes transparent to the application.
Business services should never know whether you're using:
- Bedrock
- OpenAI
- Claude
- Ollama
  only AiClient knows.