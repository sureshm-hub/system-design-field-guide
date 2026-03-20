```mermaid
---
Context Diagram
---
graph TD;
A[Event Producers] -->|Events| B[Event Orchestration System]
B -->|Processed Events| C[Consumers]
B -->|Logs & Metrics| D[Monitoring & Alerting]
    B -->|Retries & Failures| E[Dead Letter Queue]
C -->|Response/Acknowledgment| B