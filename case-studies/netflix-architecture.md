# Netflix System Design

## Problem Statement
Design a video streaming platform like Netflix that can handle millions of concurrent users.

## High-Level Architecture
- **CDN (Content Delivery Network)**
- **Microservices + API Gateway**
- **Data Storage (Metadata, User Preferences)**
- **Caching (Redis, CDN Edge Nodes)**

![Netflix Architecture](../assets/netflix-architecture.png)

## Scaling Considerations
- **Read-heavy workload** → Use caching and CDNs.
- **High availability** → Multi-region deployments.
- **Personalization** → AI-driven recommendation engine.

## Trade-offs
- **Strong consistency vs. performance:** Using eventual consistency in recommendations.
- **Cost vs. latency:** CDN usage reduces cost but requires geo-replication.  
