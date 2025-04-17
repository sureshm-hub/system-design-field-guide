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