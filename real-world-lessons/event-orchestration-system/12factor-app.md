```mermaid
graph TD
subgraph "C1: System Context"
A[Users] -->|Send Events| B[Event Orchestration System]
B -->|Processed Events| C[Consumers]
end

    subgraph "C2: Container Diagram"
        D[API Service] -->|Port Binding Factor 7| E[Message Broker Kafka/SQS]
        E -->|Processes Factor 6| F[Event Orchestration Engine]
        F -->|Backing Services Factor 4| G[Database PostgreSQL/DynamoDB]
        F -->|Concurrency Factor 8| H[Event Processing Workers]
        H -->|Logs Factor 11| I[Monitoring & Logging Datadog/ELK]
    end

    subgraph "C3: Component Diagram"
        J[CI/CD Pipeline] -->|Build, Release, Run Factor 5| K[Kubernetes Deployment]
        K -->|Dev/Prod Parity Factor 10| L[Production Cluster]
        L -->|Config Factor 3| M[Environment Variables]
        L -->|Disposability Factor 9| N[Graceful Shutdown]
        L -->|Admin Tasks Factor 12| O[DB Migrations Flyway]
    end

    subgraph "C4: Code Level"
        P[EventProcessor Class] -->|Dependencies Factor 2| Q[Maven/NPM Packages]
        P -->|Codebase Factor 1| R[Git Repository]
    end
