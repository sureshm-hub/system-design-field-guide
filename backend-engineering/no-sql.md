1. What is NoSQL?

Answer:
NoSQL stands for “Not Only SQL.” It refers to databases designed for flexible schema, horizontal scalability, high availability, and large-scale distributed workloads. Unlike traditional relational databases, NoSQL systems often relax strict relational modeling to optimize for scale, speed, or specific access patterns.

Common NoSQL categories are:

Key-value: Redis, DynamoDB

Document: MongoDB, Couchbase

Wide-column: Cassandra, HBase

Graph: Neo4j, Amazon Neptune

2. Why would you choose NoSQL over an RDBMS?

Answer:
I would choose NoSQL when:

data is semi-structured or rapidly evolving

I need horizontal scaling

application requires high write throughput

access patterns are simple and well known

I can benefit from denormalized models

global distribution and availability matter more than strict joins

I would still choose an RDBMS when I need:

complex joins

strong ACID guarantees across many entities

mature SQL analytics

strict relational integrity

So the choice is really about data shape, consistency needs, and workload patterns.

3. What are the main types of NoSQL databases?

Answer:

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
Best for social networks, fraud detection, recommendation engines, dependency mapping.

4. What is schema-less design?

Answer:
Schema-less does not mean no structure. It means the database does not enforce a rigid table schema like relational systems do. Each document or row can vary.

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
Denormalization means storing related data together instead of splitting it into many normalized tables.

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
LSM tree stands for Log-Structured Merge Tree. It is optimized for high write throughput.

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
Not automatically. Many NoSQL systems are designed for easier horizontal scaling, but the actual result depends on workload and design.

Q: Can NoSQL support transactions?
Yes, but the scope and performance characteristics vary by database.

Q: Is MongoDB schema-less?
Flexible schema, yes. But good applications still enforce schema validation and versioning.

Q: Is Cassandra good for ad hoc querying?
Not really. It is best when query patterns are known in advance.