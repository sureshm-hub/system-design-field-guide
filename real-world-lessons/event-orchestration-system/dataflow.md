```mermaid
---
Data Flow Diagram (DFD)
---

graph TD
A[Event Ingestion] --> B[Message Broker]
B -->|Streaming Events| C[Event Orchestrator]
C -->|State Updates| D[State Store DynamoDB/PostgreSQL]
C -->|Process Events| E[Workers]
E -->|Final Processed Data| F[Consumers]