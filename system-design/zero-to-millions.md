# Scale to Millions
- It is a journey!
- Build a System that start with a single user and gradually scale it to
  several million users
- Start with 1 user and continuously refine and improve endlessly!

## Single  server setup
```mermaid
flowchart LR
  U[User] --> DNS[DNS]
  DNS --> S[Single Server<br/>Web App + App Logic + DB]
```

## two-tier setup
- web & database can scale independently
- 1 to 2K user support
- Bottleneck = DB contention

```mermaid
flowchart LR
U[Users] --> DNS[DNS]
DNS --> WS[Web / App Server]
WS --> DB[(Database)]
```

### DB Choice
- Structured vs Unstructured
- Latency
- Data Volume

### Scaling DB
- Vertical vs Horizontal DB


## Load-balancer
- web tier is SPOF
- 10K users
- Bottleneck = single app server → horizontal scaling

```mermaid
flowchart LR
U[Users] --> DNS[DNS]
DNS --> LB[Load Balancer]
LB --> A1[App Server 1]
LB --> A2[App Server 2]
LB --> A3[App Server 3]
A1 --> DB[(Database)]
A2 --> DB
A3 --> DB
```

## Database Replication
- master, slave DB replication to support 
- improved performance
- R scaling and HA support
- 100K users
- Bottleneck = DB reads → replicas

```mermaid
flowchart TD
U[Users] --> LB[Load Balancer]
LB --> A1[App Server 1]
LB --> A2[App Server 2]

    A1 --> M[(Master DB)]
    A2 --> M

    M --> R1[(Read Replica 1)]
    M --> R2[(Read Replica 2)]

    A1 -. read .-> R1
    A2 -. read .-> R2
```


## Caching
- 1M users
- faster, but costlier
- cache:
  - expiration
  - eviction
  - cache frequently used data
  - consistency
- Bottleneck = latency + DB load → caching

``` mermaid
flowchart LR
U[Users] --> LB[Load Balancer]
LB --> A1[App Server 1]
LB --> A2[App Server 2]

    A1 --> C[(Cache)]
    A2 --> C

    A1 --> DB[(Database)]
    A2 --> DB
```

## CDN
- A CDN is a network of geographically dispersed servers used to deliver 
  static content. 
- CDN servers cache static content like images, videos, CSS, JS files, etc.
- Avoid global latency
- 10M users
- Bottleneck = global latency + static assets → CDN

```mermaid
flowchart LR
U[Users] --> CDN[CDN<br/>Images / CSS / JS / Video]
U --> LB[Load Balancer]

    LB --> A1[App Server 1]
    LB --> A2[App Server 2]

    A1 --> C[(Cache)]
    A2 --> C

    A1 --> DB[(Database)]
    A2 --> DB
```

## Stateless app servers
- 50M users
- true "web tier horizontal" scaling
- store session data in the persistent storage such as relational database or NoSQL
- Bottleneck = session state → stateless + shared cache

```mermaid
flowchart LR
U[Users] --> LB[Load Balancer]
LB --> A1[Stateless App Server 1]
LB --> A2[Stateless App Server 2]
LB --> A3[Stateless App Server 3]

    A1 --> Session[(Shared Session Store / Cache)]
    A2 --> Session
    A3 --> Session

    A1 --> DB[(Database)]
    A2 --> DB
    A3 --> DB
```

## Asynchronous processing with message queue
- 100M+ users
- Bottleneck = long-running tasks → async queues

```mermaid
flowchart LR
    U[Users] --> LB[Load Balancer]
    LB --> APP[Application Servers]

    APP --> MQ[[Message Queue]]
    MQ --> W1[Worker 1]
    MQ --> W2[Worker 2]

    APP --> DB[(Database)]
    W1 --> DB
    W2 --> DB
```


## Scale out Architecture
- fully distributed system
- 1B+ users
- Bottleneck = everything → full distributed system

```mermaid
flowchart TB
U[Users] --> DNS[DNS]
DNS --> CDN[CDN]
DNS --> LB[Load Balancer]

    LB --> APP1[App Server 1]
    LB --> APP2[App Server 2]
    LB --> APP3[App Server N]

    APP1 --> Cache[(Distributed Cache)]
    APP2 --> Cache
    APP3 --> Cache

    APP1 --> MQ[[Message Queue]]
    APP2 --> MQ
    APP3 --> MQ

    MQ --> Workers[Background Workers]

    APP1 --> DBM[(Primary DB)]
    APP2 --> DBM
    APP3 --> DBM

    DBM --> DBR1[(Replica 1)]
    DBM --> DBR2[(Replica 2)]

    Workers --> DBM
```


https://chatgpt.com/g/g-p-698f44b7ee788191823229d54bda6877-tech-interview/c/69cb4b1c-a900-8326-8386-af5141086bf3