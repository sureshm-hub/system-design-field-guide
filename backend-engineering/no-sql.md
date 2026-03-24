1. What is NoSQL?
- **Answer:** NoSQL stands for “Not Only SQL.” It refers to databases designed 
for flexible schema, horizontal scalability, high availability, and large-scale
distributed workloads. Unlike traditional relational databases, NoSQL systems
often relax strict relational modeling to optimize for scale, speed, or 
specific access patterns. Common NoSQL categories are:
  - **Key-value:** Redis, DynamoDB
  - **Document:** MongoDB, Couchbase
  - **Wide-column:** Cassandra, HBase
  - **Graph:** Neo4j, Amazon Neptune

2. Why would you choose NoSQL over an RDBMS?
- **Answer:** I would choose NoSQL when:
  - data is semi-structured or rapidly evolving
  - I need horizontal scaling
  - application requires high write throughput
  - access patterns are simple and well known

I can benefit from denormalized models, global distribution and availability 
matter more than strict joins.

I would still choose an RDBMS when I need:
  - complex joins
  - strong ACID guarantees across many entities
  - mature SQL analytics
  - strict relational integrity

So the choice is really about data shape, consistency needs, and workload 
patterns.

3. What are the main types of NoSQL databases?
- **Answer:**

Key-value

Stores data as key -> value.
Best for caching, sessions, counters, user preferences.

Document

Stores JSON-like documents.
Best for content systems, product catalogs, profiles, event payloads.

Wide-column

Stores rows with flexible columns grouped into column families.
Best for large-scale writes, time-series, IoT, logs.

Graph

Stores nodes and relationships.
Best for social networks, fraud detection, recommendation engines, dependency 
mapping.

4. What is schema-less design?

Answer:
Schema-less does not mean no structure. It means the database does not enforce 
a rigid table schema like relational systems do. Each document or row can vary.

This helps when:

fields change frequently

data comes from multiple sources

teams iterate quickly

But it also shifts responsibility to the application:

validate structure

manage versioning

prevent inconsistent documents

So schema flexibility is powerful, but it needs governance.

5. What is denormalization in NoSQL?

Answer:
Denormalization means storing related data together instead of splitting it 
into many normalized tables.

Example:
Instead of separate orders, customers, and addresses tables joined at query time, a document DB may store the order with embedded customer snapshot and shipping address.

Benefits:

faster reads

fewer joins

better performance for known query patterns

Tradeoff:

duplication

more complex updates

possible data inconsistency if not handled carefully

In NoSQL, data modeling is usually query-first, not normalization-first.

6. What is eventual consistency?

Answer:
Eventual consistency means that after a write, not all replicas may reflect the new value immediately, but they will converge over time.

This is common in distributed NoSQL systems because it improves:

availability

write performance

partition tolerance

Example:
A user updates profile data in one region. Another region may briefly serve the old version before replication catches up.

This is acceptable for use cases like:

social feeds

analytics

product views

But less acceptable for:

account balances

critical transactional workflows

7. Explain CAP theorem.

Answer:
CAP theorem says that in the presence of a network partition, a distributed system can provide only one of:

Consistency

Availability

Partition tolerance

Since partitions are unavoidable in distributed systems, real systems usually choose between:

CP: favor consistency over availability

AP: favor availability over immediate consistency

Important nuance:
CAP is specifically about behavior during a partition, not normal operation.

Example:

Cassandra is typically more AP-oriented

HBase is more CP-oriented

8. What is the difference between ACID and BASE?

Answer:

ACID

Atomicity

Consistency

Isolation

Durability

Used in relational systems for strong transactional guarantees.

BASE

Basically Available

Soft state

Eventual consistency

Used in many NoSQL systems to support scalability and availability.

I’d explain that NoSQL is not “bad at transactions”; rather, many NoSQL systems choose different tradeoffs for distributed scale.

9. What is sharding?

Answer:
Sharding means splitting data across multiple nodes using a shard key.

Example:

customer data shard by customer_id

log data shard by date or tenant

Benefits:

horizontal scale

distributes read/write load

avoids single-node bottleneck

Challenges:

bad shard key can create hotspots

resharding is expensive

cross-shard queries are harder

A good shard key has:

high cardinality

even distribution

alignment with query patterns

10. What is replication in NoSQL?

Answer:
Replication means maintaining copies of data across nodes.

Benefits:

high availability

fault tolerance

read scaling

Common models:

leader-follower

peer-to-peer

multi-leader

Tradeoffs involve:

replication lag

conflict resolution

consistency guarantees

For example, Cassandra uses peer-based replication with tunable consistency.

11. What is a good partition key in Cassandra/Dynamo-style systems?

Answer:
A good partition key should:

distribute data evenly

avoid hotspots

support common access patterns

not create oversized partitions

Bad example:

partition key = country if most traffic is US

Good example:

customer_id

or composite keys like tenant_id + bucket

I’d always validate partition design against:

read patterns

write volume

cardinality

skew

retention period

12. What are hotspots in NoSQL systems?

Answer:
Hotspots happen when too much traffic goes to one partition, shard, or node.

Causes:

poor partition key

monotonically increasing keys

highly popular tenants/items

Effects:

throttling

higher latency

uneven cluster utilization

Mitigations:

better shard key

write bucketing

random suffix/prefix

caching

load-aware partitioning

13. How is data modeling different in NoSQL?

Answer:
In RDBMS, modeling usually starts with entities and normalization.
In NoSQL, modeling often starts with access patterns.

So I ask:

what queries are most frequent?

what response time is needed?

what is the write/read ratio?

what data is retrieved together?

do I need aggregation, range scans, or graph traversal?

Then I shape the document/partition to minimize expensive distributed operations.

14. What are secondary indexes in NoSQL?

Answer:
Secondary indexes allow querying by fields other than the primary key.

They improve flexibility, but they may introduce:

extra write cost

storage overhead

potential performance unpredictability at scale

In large systems, many teams prefer:

primary-key-driven access

carefully designed GSIs/LSIs in DynamoDB

materialized views

inverted indexes via search systems

So secondary indexes are useful, but not free.

15. What is the difference between MongoDB and Cassandra?

Answer:

MongoDB

document database

rich querying

flexible JSON documents

easier for developer productivity

good for content/catalog/profile use cases

Cassandra

wide-column distributed database

optimized for massive write throughput

highly available across regions

query patterns must be designed carefully up front

strong for time-series, logs, telemetry, event storage

MongoDB gives more query flexibility.
Cassandra gives stronger large-scale write and availability characteristics.

16. What is tunable consistency?

Answer:
Tunable consistency means the client can choose how many replicas must acknowledge reads or writes.

Example in Cassandra:

ONE

QUORUM

ALL

This lets you balance:

latency

availability

consistency

For instance:

write at QUORUM, read at QUORUM for stronger consistency

write at ONE, read at ONE for low latency but more stale-read risk

17. What are read repair and hinted handoff?

Answer:

Read repair

When replicas disagree during a read, the system detects stale copies and updates them.

Hinted handoff

If a replica is temporarily unavailable during a write, another node stores a hint and forwards it later.

These mechanisms help distributed replicas converge over time.

18. What is compaction?

Answer:
Compaction is the process of merging and reorganizing on-disk data files to improve read efficiency and reclaim space.

In LSM-tree-based databases like Cassandra:

writes go to memtable

flushed to SSTables

many SSTables accumulate

compaction merges them

Tradeoffs:

improves reads

removes obsolete versions/tombstones

consumes CPU, disk I/O, and space during execution

19. What are tombstones?

Answer:
A tombstone is a delete marker. In many distributed NoSQL systems, deletes are not immediately removed from disk. Instead, a tombstone is written and later cleaned up during compaction.

Why:

replicas may be out of sync

delete must propagate safely

Problem:

too many tombstones can hurt reads and compaction performance

So TTL-heavy or delete-heavy workloads need careful design.

20. What is TTL?

Answer:
TTL means time to live. It defines how long data should remain before expiring automatically.

Common use cases:

sessions

cache entries

temporary events

retention-controlled logs

Benefits:

simpler cleanup

automatic expiration

Tradeoff:

lots of TTL expirations can create tombstones in some systems

21. How do transactions work in NoSQL?

Answer:
It depends on the database.

Historically, many NoSQL systems supported:

atomicity at a single document/row/partition level

but not multi-record distributed transactions

Modern NoSQL systems may support broader transactions, but usually with tradeoffs in latency and scalability.

Interview answer:

use single-record atomic updates when possible

model data to keep related writes together

use idempotency, outbox, saga, and compensation for cross-entity workflows

22. What is idempotency and why is it important in NoSQL/distributed systems?

Answer:
Idempotency means repeating the same operation produces the same result.

Important because distributed systems may retry due to:

timeouts

network failures

partial acknowledgments

Example:
If an order creation request is retried, an idempotency key prevents duplicate orders.

This is especially important when eventual consistency and retries are part of normal operation.

23. How would you model a user profile in a document database?

Answer:
I’d likely store one document per user:

user id

name

contact info

settings

preferences

maybe recent addresses or devices

I would embed fields that are read together often.
I would avoid unbounded arrays if they grow very large, like all login history forever.

So:

embed small, bounded substructures

separate high-volume append-only data into another collection

24. When should you not use NoSQL?

Answer:
I would avoid NoSQL when:

the domain is highly relational

queries need many joins and ad hoc SQL

strong cross-entity transactional consistency is essential

reporting and BI heavily depend on relational structures

data integrity constraints are central

In those cases, an RDBMS may be simpler and safer.

25. What are common NoSQL use cases?

Answer:
Common use cases include:

user sessions and caching

product catalogs

content management

event logging

telemetry/time-series

recommendation graphs

shopping carts

profile and preference stores

IoT data ingestion

fraud/network analysis

26. What are the challenges with NoSQL?

Answer:
Main challenges are:

weaker join capabilities

denormalization complexity

consistency tradeoffs

query limitations

more careful key/partition design

operational complexity in distributed clusters

potential vendor-specific APIs and patterns

So NoSQL gives scale and flexibility, but pushes more design responsibility to engineers.

27. How do you handle relationships in NoSQL?

Answer:
Usually through:

embedding

referencing by ID

precomputed/materialized views

application-side joins

Choice depends on cardinality and access pattern.

Example:

one-to-one or bounded one-to-few: embed

large one-to-many: separate collection/table

many-to-many with traversal-heavy workload: graph DB may fit better

28. What is an LSM tree and why is it used?

Answer:
LSM tree stands for Log-Structured Merge Tree. It is optimized for high write 
throughput.

Basic flow:

write goes to memory

also often to commit log

flushed to immutable disk files

later merged by compaction

Benefits:

fast sequential writes

good for heavy-ingest workloads

Tradeoff:

read amplification

compaction overhead

Many NoSQL systems use this because they prioritize large-scale writes.

29. How would you design a URL shortener using NoSQL?

Answer:
A simple design:

key-value store

short code as key

original URL and metadata as value

Why NoSQL works:

lookup by key is primary operation

huge scale possible

simple read/write pattern

Need to think about:

code generation strategy

TTL if links expire

caching hot keys

analytics in separate pipeline

multi-region replication if global

30. What is the biggest mistake engineers make with NoSQL?

Answer:
Treating NoSQL like a relational database.

Common symptoms:

trying to do too many joins

ignoring access patterns

poor partition keys

underestimating denormalization cost

assuming schema-less means no governance

The right mindset is:
design around queries, scale behavior, and consistency tradeoffs.

A few rapid-fire interview one-liners

Q: Is NoSQL always eventually consistent?
No. Some NoSQL systems support strong consistency or tunable consistency.

Q: Is NoSQL more scalable than SQL?
Not automatically. Many NoSQL systems are designed for easier horizontal 
scaling, but the actual result depends on workload and design.

Q: Can NoSQL support transactions?
Yes, but the scope and performance characteristics vary by database.

Q: Is MongoDB schema-less?
Flexible schema, yes. But good applications still enforce schema validation 
and versioning.

Q: Is Cassandra good for ad hoc querying?
Not really. It is best when query patterns are known in advance.

https://chatgpt.com/g/g-p-698f44b7ee788191823229d54bda6877-tech-interview/c/69b3a5fa-ec40-832e-b907-ed2186afd6fd

Q: Monotonically increasing keys cause hot spots, why?
A: While these keys are easy to generate and manage, they often cause 
significant performance issues in distributed databases like MongoDB, Google 
Cloud Spanner, and HBase.
 Single-Server Bottlenecks: Because new keys are always larger than old ones,
they are routed to the same "end" of the database (the last shard or partition). 
This causes a single server to handle all write traffic while others sit idle,
a problem known as hotspotting.

Indexing Inefficiency: In some systems, these keys can lead to poorly balanced 
index nodes because splits only happen at the "right edge" of the index.

mitigation can be done through salting, hashing, compound keys (monotonically 
increasing sequence + high cardinality random field).

Q: Apache HBase?
A: HBase is a NoSQL distributed database that runs on top of the Hadoop 
ecosystem (HDFS).  It provides real-time read/write access to large datasets 
using a row-based key-value model.

HBase Architecture Overview: 
  - HMaster
  - Region Servers
  - Regions
  - ZooKeeper
  - HDFS
  - Meta Table

What happens in case of network partitions or failures:

If the HBase master is partitioned or fails, ZooKeeper elects a new master, 
and until that master is fully up, cluster management operations like region 
reassignment and table metadata changes are affected. Existing regions usually 
keep serving reads and writes through their RegionServers, but any regions 
that need reassignment during this window can become temporarily unavailable.

Because HBase prioritizes Consistency and Partition Tolerance (CP), when there
is a network partition or a region‑server failure, HBase will block reads and
writes for the affected regions until it can safely reassign them and ensure a
consistent view of data, temporarily sacrificing availability.

If HMaster fails → ZooKeeper elects a standby HMaster.
If a Region Server fails → ZooKeeper notifies HMaster, which reassigns its 
regions to other servers.

Data durability is ensured via WAL + HDFS replication.

How Data Flows
- Apache HBase uses a Log-Structured Merge-tree (LSM-tree) architecture for
  its storage engine. This design is particularly well-suited for high write 
  throughput in big data scenarios.
- Write Path: Data first enters MemStore (in-memory buffer) and is logged in 
  Write-Ahead Log (WAL) for durability. Once MemStore reaches a threshold, data
  is flushed to disk as HFiles.
- Read Path: Queries first check BlockCache (in-memory read cache). If not
  found, data is fetched from HFiles on disk.
- Compaction: Over time, small HFiles are merged into larger files to optimize
  read performance.
  - Minor compaction: Merges small files.
  - Major compaction: Merges all files — more resource-intensive but reduces 
    fragmentation.

Core Concepts of HBase
Row Key : Uniquely identifies each row. It’s sorted in ascending order and 
determines data access patterns.

Column Family: A logical grouping of related columns. Each family is stored 
separately, enabling flexible schema design.

Column & Versioning: Each column belongs to a family and supports multiple 
versions using timestamps.

Sparse and Dynamic Storage: HBase tables can have variable columns per row, 
saving space and allowing dynamic schema evolution.

Q: Cassandra?
A: fundamentally an AP system, prioritizing constant uptime and uninterrupted
service (Availability) and resilience to node failures (Partition Tolerance)
over immediate, strict consistency.

This answers the question why we need Cassandra in our application. 
Applications that demand zero downtime need a masterless architecture and 
that’s where Cassandra drives the value. In simple words, Write and Read can 
happen from any node in the cluster at any point in time. The below example 
shows the sample cluster formation in Cassandra for 5 node setup.

Better Throughput:  As per Cassandra, if two nodes can handle 100K transactions
per second, then 4 nodes can handle 200K transactions per second and the 
strength multiplies so on.
https://www.aziro.com/en/blog/role-of-apache-cassandra-in-the-cap-theorem

* Key Aspects of Cassandra's AP Nature
- Leaderless Architecture: Unlike CP systems with a master, all Cassandra nodes
are equal, allowing any node to handle read and write requests to ensure
high availability.
- High Write Throughput: Designed for heavy, fast writes, making it ideal for 
distributed applications where data needs to be recorded across multiple 
data centers.
- Tunable Consistency: While primarily AP, Cassandra allows users to trade 
off availability for consistency on a per-query basis by adjusting replication 
factors and consistency levels.
- type of NoSQL: can readily serve as a key-value store, but more accurately 
classified as a wide-column store.
- Key-Value Aspects of Cassandra
  Data Access: To retrieve data in Cassandra, you use a row key (also called a
  partition key), which acts as the "key" in a key-value pair.
  Storage: The value associated with the key is the collection of columns 
  (a row) that physically reside together.
- The primary difference from a simple key-value store is that within the 
"value" (the row), data is organized into "column families" with flexible 
schemas. Each row can have a different set of columns, and columns have a name, 
value, and timestamp
- In essence, Cassandra uses a "key-key-value" mechanism, where the first key 
is the partition key and the second key (clustering key) orders data within 
that partition

**flexible schema:**
- Yes, even though Cassandra has a flexible schema, you must still define a 
schema. Unlike "schema-less" databases (like MongoDB) where you can often 
insert any JSON document into a collection without prior setup, Cassandra 
requires you to define a Keyspace and Table before any data can be written.
Why "Flexible" doesn't mean "Schema-less"
 - Mandatory Primary Keys: Every table must have a primary key defined upfront. 
   This is non-negotiable because it determines how data is distributed across 
   the cluster.
 - Column Definitions: While you can add new columns later, a column must be
   part of the table's definition before you can insert data into it.
 - Sparse Rows: The "flexibility" comes from the fact that not every row needs
   to have a value for every column. In a traditional database, empty columns
   still take up some overhead; in Cassandra, if a row doesn't have data for 
   a column, that column simply isn't stored for that row.

Best Practices for Cassandra Schema Design:
Because Cassandra does not support complex joins, your schema design is
query-driven.

Identify Queries First: You should design your tables based on the specific 
questions your application will ask, rather than modeling based on 
real-world entities.

One Table Per Query: It is common and recommended to denormalize data—meaning 
you might store the same data in three different tables to support three 
different types of lookups.

Use Collections for Minor Variations: If you have semi-structured data that 
changes frequently (like user preferences), you can use Map, Set, or List 
types within a single column to gain extra flexibility without constantly 
altering your schema.

Cassandra provides native support for LIST, SET, and MAP collection data 
types, which are used to store multiple items within a single column of a 
table. These collections help with data denormalization by grouping related 
data, such as a user's multiple email addresses, without requiring a separate 
table and join operations.

Important Considerations
Frozen vs. Non-Frozen: Collections can be defined as FROZEN or non-frozen.
Non-frozen (default) allows individual elements to be updated or deleted.
FROZEN collections are serialized as a single value and must be updated or 
replaced in their entirety.

https://vicxu.medium.com/why-is-cassandra-suitable-for-high-write-workloads-5151f0589fb6

### Cassandra Internals
This immediately raises two questions: If there’s no leader, how do nodes find 
each other? And how does a node know where a specific piece of data is supposed 
to live?

The Gossip Protocol: Nodes discover the state of the cluster by “gossiping.” 
Periodically, each node exchanges state information (like its health and data
load) with a few other random nodes. This information spreads exponentially 
through the cluster like a rumor.

The Consistent Hashing Ring: Data is distributed across the cluster using a 
clever technique called consistent hashing. In simple terms, imagine a massive 
circle or “ring”. Each

Replication Factor (RF)
You never store just one copy of your data. You configure a Replication 
Factor — typically RF=3

Tunable Consistency: The User is in Control
Let’s walk through a write request with RF=3

CL.ONE: The coordinator only needs an acknowledgment from one of the three replicas
CL.QUORUM: The coordinator waits for a majority of replicas to respond. For RF=3, a quorum is (3/2) + 1 = 2
CL.ALL: The coordinator must wait for all three replicas to acknowledge the write.

Embracing Eventual Consistency: 
The consequence of prioritizing availability is that data across replicas can
temporarily become inconsistent. Cassandra is designed to accept this reality 
and then fix it. This is called eventual consistency.

https://sameerahmed56.medium.com/the-swarm-of-availability-a-deep-dive-into-cassandras-ap-architecture-c67d46720736


**Relational DB:**
- Relational databases (RDBMS) are generally CP (Consistency + Partition 
Tolerance) on the CAP theorem.
- They prioritize ACID compliance—ensuring all nodes have identical, accurate 
data—over being available during a network partition.
- Relational Databases (RDBMS) and HBase are not generally categorized the same
way under the CAP theorem. HBase is a classic CP system (Consistency + 
Partition Tolerance), sacrificing availability during network splits. RDBMS 
are typically CA (Consistency + Availability), aiming for full consistency 
and uptime, but struggle with horizontal scaling (Partition Tolerance) 
compared to distributed NoSQL

DB-Engines Ranking: https://db-engines.com/en/ranking

**shard key vs partition key vs cluster key:**
Shard keys, partition keys, and cluster keys are database design techniques 
used to manage large datasets by dividing data for better performance and 
scalability. 
- Shard keys distribute data across multiple physical servers
  - physical separate of data
- partition keys split data into logical chunks within one server
  - Logical Separation of Data
- cluster keys sort data inside those partitions
  - organizing data with in the logical separation
- **Common Pitfalls:**
 - Hotspots: Choosing a key with low cardinality (few unique values) or 
   non-uniform distribution causes one shard/partition to be overloaded.
 - Monotonic Keys: Using sequential IDs or timestamps as keys can lead to 
   overloading the newest shard.
- not all No SQL DB's have shard key exposed, e.g., DynamoDB (DynamoDb streams
  have write shards for increasing throughput).

### CAP:
- Consistency means that any read request will return the most recent write
- Availability means that a non-responding node must respond in a reasonable 
  amount of time. 
- partition tolerance, which states the system will continue to operate 
  despite network or node failures.
- ACID vs BASE: Basically available, soft state and Eventually consistent
https://thomaslarock.com/2018/07/databases-101/#acid