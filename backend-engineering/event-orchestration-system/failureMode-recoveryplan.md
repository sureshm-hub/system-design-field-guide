```mermaid
---
Failure Mode & Recovery Plan
---

graph TD
    A[Event Ingestion] -->|Network Failure?| B[Retry Mechanism]
    B -->|Success| C[Process Event]
    B -->|Failure After Retries| D[Dead Letter Queue]
    C -->|Database Failure?| E[State Store Retry]
    E -->|Success| F[Update Event Status]
    E -->|Multiple Failures| G[Alert & Fallback]
