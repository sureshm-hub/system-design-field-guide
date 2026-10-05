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

# PACELC
- Extends this by adding that even when running normally (Else), systems must 
trade off Latency (L) and Consistency (C)

- CAP is 1% of story, PACELC is 99% 

## CAP Theorem Breakdown (When Partitioned)
- CP (Consistency/Partition Tolerance): Data is consistent, but system might be unavailable.
- AP (Availability/Partition Tolerance): System is available, but data might be stale (eventual consistency).

## PACELC Breakdown (When Normal)
- ELC: Else (no partition), choose between Latency or Consistency.
- PA/EL: If Partition, choose Availability; Else, choose Low Latency (e.g., DynamoDB).
- PC/EC: If Partition, choose Consistency; Else, choose Consistency (e.g., strict SQL systems).

- PACELC is generally considered a more comprehensive and modern tool for evaluating distributed system trade-offs. 

# ACID (Atomicity, Consistency, Isolation, Durability) 
- ACID & BASE models provide guidance for designing transactional systems and
  dealing with the challenges of eventual consistency in distributed databases.

 ## Isolation Levels: Databases offer varying levels of isolation, allowing a trade-off between performance and data accuracy:

- Serializable: The highest level of isolation, ensuring transactions are 
executed as if they were in a strict, sequential order.

- Repeatable Read: Guarantees that if a transaction reads data once, it can 
read it again and find the same values.

- Read Committed: Ensures that only data that has been committed is 
read, preventing "dirty reads".

- Read Uncommitted: The lowest level, allowing dirty reads.

### Core Differences:
- Read Committed (RC): Each individual statement within a transaction sees a new snapshot of the data. This means if another transaction commits changes between your first and second query, your second query will see those new values.
- Repeatable Read (RR): The entire transaction uses a single snapshot taken at the start. Even if other transactions commit changes, you will continue to see the data exactly as it was when your transaction began.

# BASE (Basically Available, Soft-state, Eventually Consistent) models

- BA: System is basically available
- Soft State: State of the system changes over time even w/o external input
- E: Eventually consistent

### Key Characteristics of Soft State
- Background Updates: The system's state may change automatically due to background processes, such as data replication or synchronization between nodes, rather than direct user action.
- Transient Inconsistency: Different replicas of the same data may temporarily hold different values (e.g., Server A shows 10 likes, while Server B shows 12).
- Lack of Strong Guarantees: Because the state is "soft," there is no guarantee that a read operation will return the most recent write at any given moment.

https://blog.bytebytego.com/p/cap-pacelc-acid-base-essential-concepts