# CAP 
NoSQL databases are often evaluated in the context of the CAP Theorem, which states that a distributed database can guarantee only two of the following three properties at any given time:

**Consistency (C)** – Every read receives the most recent write or an error.
**Availability (A)** – Every request (read or write) receives a response, even if some nodes are down.
**Partition Tolerance (P)** – The system continues to function even if network partitions (communication failures between nodes) occur.

Since network partitions (P) are unavoidable in distributed systems, NoSQL databases typically make a tradeoff between Consistency (C) and Availability (A), leading to two major categories:

## 1. CP (Consistency + Partition Tolerance)
   Prioritizes strong consistency over availability.
   If a network partition occurs, some nodes may become unavailable to ensure all reads get the latest data.
   Example NoSQL Databases:
   HBase (Column-family store)
   MongoDB (in strong consistency mode)
   Redis (with replication and strong consistency)

## 2. AP (Availability + Partition Tolerance)
   Prioritizes availability, allowing the system to serve requests even if some nodes have stale data.
   Trades off strong consistency for eventual consistency.
   Example NoSQL Databases:
   Cassandra (Column-family store)
   DynamoDB (Key-Value store)
   Riak

## 3. CA (Consistency + Availability) – Theoretical Only
   This combination is impossible in a distributed system because it assumes no network partitions, which is unrealistic.
   Choosing a NoSQL DB Based on CAP Theorem

   If **strong consistency** is critical (e.g., financial applications), choose **CP databases**.
   If **high availability** is more important (e.g., social media, caching), choose **AP databases**.

Most NoSQL databases provide **tunability** between consistency and availability (e.g., MongoDB, Cassandra allow adjusting replication settings).

## 4. Typical relational databases 
Typical relational databases like Oracle, MySQL, PostgreSQL, and SQL Server are **traditionally not designed for distributed environments**, meaning the CAP theorem doesn’t apply to them in the same way as NoSQL databases. 
However, when they are **configured in a distributed setup (like replication, clustering, or sharding)**, their behavior can be classified under CAP properties.

## Relational Databases in a Distributed Setup: CP vs. CA
### Single-node RDBMS (Traditional Mode) – Neither CP nor AP

A standalone relational database running on a single server does not face network partitions.
It guarantees Consistency (C) and Availability (A) as long as the server is up.
Since partition tolerance (P) is irrelevant, CAP theorem doesn’t apply.

### Distributed RDBMS (e.g., MySQL Cluster, Oracle RAC, Google Spanner) – CP Mode

When an RDBMS is deployed in a **distributed setup (multi-node replication, clustering, or consensus-based transactions)**, it typically prioritizes Consistency (C) over Availability (A).

### Example systems:
- Google Spanner (CP) – Uses global transactions with TrueTime to ensure strong consistency.
- MySQL Group Replication (CP) – Uses consensus protocols (Paxos/Raft) to enforce consistency.
- Oracle RAC (Real Application Clusters) (CP) – Ensures consistency but may reject requests if nodes become unavailable.

### Distributed RDBMS with High Availability (CA Tendency)

Some relational databases **trade off strong consistency** for better availability by using:
- **Asynchronous replication** (e.g., MySQL with read replicas)
- **Eventual consistency** (e.g., Oracle GoldenGate)

In such setups, they lean more toward CA, but strict CA is impossible in a distributed system with network partitions.

### Summary of Consistency Models

#### Strong consistency provides accuracy but comes with latency, cost, and availability trade-offs. For many real-world apps (like social feeds), eventual or causal consistency is more efficient and acceptable.

#### Eventual consistency still guarantees convergence across replicas over time. It’s predictable, just not immediate. It's ideal for high-availability systems where slight staleness is tolerable.

#### Causal consistency is very practical in real-world systems like collaborative tools, messaging, and social apps, where operation order matters.

#### You must choose trade-offs during partitions, not always. The system can be CA when no partitions exist. The CAP theorem only kicks in under network partitions.

#### Many modern systems (like Cosmos DB, Cassandra, DynamoDB) support multiple consistency models, and let you tune per operation or per session.

#### Consistency deals with visibility of data across replicas, not durability (persistence) or availability (uptime). They are separate guarantees in distributed systems.

```mermaid

%%{init: {"theme":"default"}}%%
table
  title Distributed Systems Consistency Models

  header
    System Model
    Default Consistency Model
    Description
    Ideal Use Cases
    How to Tune
    Notes

  row
    Amazon DynamoDB
    Eventual (reads)
    Fully managed NoSQL DB; writes are strongly consistent
    IoT, leaderboards, session stores
    Use `ConsistentRead=True` or `--consistent-read`
    Global Tables = eventual across regions

  row
    Apache Cassandra
    Tunable
    Decentralized NoSQL DB with quorum-based consistency
    Logging, time-series, recommendations
    Use consistency levels: ONE, QUORUM, ALL
    Developer manages trade-offs

  row
    MongoDB
    Eventual (replica sets)
    Document DB; supports causal via sessions
    CMS, analytics, catalogs
    Use `readConcern=majority` or sessions
    Strong reads only from primary

  row
    etcd
    Strong
    Key-value store using Raft consensus
    Config mgmt, service discovery
    Not tunable
    Strong only; limited scalability

  row
    Zookeeper
    Strong
    Coordination service using ZAB protocol
    Leader election, config storage
    Not tunable
    Often replaced with etcd

  row
    Redis
    Strong (standalone), Eventual (clustered)
    In-memory store with variable consistency
    Caching, pub/sub, game state
    Choose standalone vs. cluster
    Cluster = faster, less consistent

  row
    CockroachDB
    Strong
    SQL-compliant DB with distributed consensus
    Multi-region RDBMS apps
    Not tunable
    Optimized for correctness

  row
    Google Spanner
    Strong
    Global DB with TrueTime-based external consistency
    SaaS, finance, global inventory
    Staleness options available
    Expensive but precise

  row
    Azure Cosmos DB
    Session (default)
    Multi-model DB with 5 consistency levels
    E-commerce, ML, multi-tenant SaaS
    Choose from 5 options: Strong to Eventual
    Most tunable platform

  row
    Riak
    Eventual
    Key-value store with quorum tuning
    Fault-tolerant, IoT ingestion
    Tune via N/R/W settings
    Use CRDTs for conflict resolution

  row
    Google BigTable
    Strong (row), Eventual (multi-row)
    Wide-column store for big data
    IoT, analytics pipelines
    Schema design keeps data in one row
    Multi-row ops = eventual
    
```
