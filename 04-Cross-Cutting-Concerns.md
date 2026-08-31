
## Cross-Cutting Concerns

Implemented:

- Correlation IDs
```java
Correlation Filter

↓

MDC

↓

Execution Timer

↓

Logs
```
- Execution Timer
```java
ExecutionTimer.measure(...)
```
reusable, clean.

- Audit Publisher

These features demonstrate that AI services should be observable just like any other enterprise service.
