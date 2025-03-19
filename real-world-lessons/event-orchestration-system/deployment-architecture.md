```mermaid
---
Deployment Architecture
---

graph TD
subgraph AWS Cloud
subgraph Region A
subgraph VPC
A[API Gateway] --> B[Event Orchestration Service (ECS/Kubernetes)]
B --> C[Message Broker (Kafka/SQS)]
C --> D[State Store (DynamoDB/PostgreSQL)]
B --> E[Monitoring & Logging (CloudWatch/Datadog)]
end
end
subgraph Region B (Failover)
F[Replica Orchestration Service]
F --> G[Replica State Store]
end
end
