```mermaid
---
Component Diagram
---

graph TD;
    subgraph Event Orchestration System
        A[Event Ingestion API] --> B[Message Broker (Kafka/SQS)]
        B --> C[Orchestration Engine]
        C --> D[State Management (Postgres/DynamoDB)]
        C --> E[Event Processing Workers]
    end
    F[Monitoring & Logging] -->|Observability| C
    G[Consumers (Microservices, Analytics)] -->|Consume Events| E
