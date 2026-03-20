```mermaid
---
Sequence Diagram
---

sequenceDiagram
    participant Producer
    participant API as Event Ingestion API
    participant Broker as Message Broker (Kafka/SQS)
    participant Orchestrator as Orchestration Engine
    participant Worker as Event Processing Worker
    participant Consumer

    Producer->>API: Sends Event
    API->>Broker: Publishes Event
    Broker->>Orchestrator: Delivers Event
    Orchestrator->>Worker: Dispatches Processing Task
    Worker->>Consumer: Sends Processed Event
    Worker->>Orchestrator: Acknowledgment & State Update
