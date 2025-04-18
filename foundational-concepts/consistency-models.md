# Consistency Model Trade-offs

- Strong consistency provides accuracy but comes with latency, cost, and availability trade-offs. For many real-world apps (like social feeds), eventual or causal consistency is more efficient and acceptable.

- Eventual consistency still guarantees convergence across replicas over time. It’s predictable, just not immediate. It's ideal for high-availability systems where slight staleness is tolerable.

- Causal consistency is very practical in real-world systems like collaborative tools, messaging, and social apps, where operation order matters.

- You must choose trade-offs during partitions, not always. The system can be CA when no partitions exist. The CAP theorem only kicks in under network partitions.

- Many modern systems (like Cosmos DB, Cassandra, DynamoDB) support multiple consistency models, and let you tune per operation or per session.

- Consistency deals with visibility of data across replicas, not durability (persistence) or availability (uptime). They are separate guarantees in distributed systems.

## Summary of Consistency Models

| System Model         | Default Consistency Model       | Description                                                       | Ideal Use Cases                                | Tuning Method                                                                                                                                                             | Notes                                                   |
|----------------------|----------------------------------|-------------------------------------------------------------------|-------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| Amazon DynamoDB      | Eventual Consistency (reads)     | NoSQL, low-latency DB with optional strong read                  | IoT, shopping carts, leaderboards               | `ConsistentRead=True` or `--consistent-read`                                                                                                                              | Writes are strongly consistent by default               |
| Apache Cassandra     | Tunable Consistency              | Decentralized DB with read/write quorum levels                   | Logging, metrics, time-series                   | Use consistency levels:<br/> ONE - Fastest, least consistent,<br/> QUORUM - N/2+1  Balanced consistency and performance,<br/> ALL - Highest consistency, slowest performance | Developer defines consistency trade-offs                |
| MongoDB              | Eventual (Replica Sets)          | Document store with causal/session consistency options           | CMS, analytics, content                         | Use sessions, `readConcern: "majority"`                                                                                                                                   | Strong only on primary node                            |
| etcd                 | Strong Consistency               | Raft-based key-value store for distributed coordination           | Service discovery, config store                 | Not tunable                                                                                                                                                               | Always linearizable                                     |
| Zookeeper            | Strong Consistency               | ZAB-based coordination system                                    | Leader election, locks                          | Not tunable                                                                                                                                                               | Can be a single point of failure                        |
| Redis                | Strong (Standalone), Eventual (Clustered) | In-memory DB with HA cluster support                             | Caching, pub/sub, sessions                      | Use standalone (strong) or replica (eventual)                                                                                                                             | Clustered Redis is eventually consistent                |
| CockroachDB          | Strong Consistency               | Distributed SQL DB with global transactions                      | Banking, fintech, multi-region apps             | Not tunable                                                                                                                                                               | Strong across nodes and regions                        |
| Google Spanner       | Strong Consistency               | Global SQL DB with external consistency via TrueTime             | Global inventory, finance                        | Can use stale reads (`bounded staleness`)                                                                                                                                 | Strong across continents                               |
| Azure Cosmos DB      | Session Consistency              | Multi-model DB with 5 tunable models                             | Personalization, e-commerce                     | Choose among Strong, Bounded, Eventual, etc.                                                                                                                              | Most flexible consistency options available             |
| Riak                 | Eventual Consistency             | KV store prioritizing availability                               | Sensor data, CRDT-based systems                 | Use quorum settings: N, R, W                                                                                                                                              | CRDTs needed for conflict resolution                    |
| Google BigTable      | Strong (row), Eventual (multi-row) | Column DB with row-based strong consistency                     | Analytics, telemetry                            | Design schema to keep strong consistency within a row                                                                                                                     | Multi-row ops are eventually consistent                |


### Best Practices:

| Need	                           | Recommended Tuning                                      |  
|---------------------------------|---------------------------------------------------------|
| Accurate reads after writes     | 	Strong (QUORUM/ALL) consistency for read/write         |
| Low latency, ok to lag          | 	Eventual or single-node reads                          |
| Per-user causality	             | Session or Causal Consistency                           |
| Tradeoffs between cost/latency	 | Bounded staleness or Consistent Prefix                  |
| Geo-distributed apps	           | Spanner or Cosmos DB with strong or session consistency |


## Special Focus: DynamoDB Consistency Options
DynamoDB supports two types of reads:

Eventually Consistent Reads (default): Faster, cheaper, might return stale data

Strongly Consistent Reads: Always returns the most recent data, with slightly higher latency and cost

For writes, all operations are strongly consistent by default.

``` java
Read Consistency in AWS SDK
 
DynamoDbClient dynamoDb = DynamoDbClient.create();

GetItemRequest request = GetItemRequest.builder()
.tableName("Users")
.key(Map.of("userId", AttributeValue.fromS("123")))
.consistentRead(true) // 👈 Enables strong consistency
.build();

GetItemResponse response = dynamoDb.getItem(request);
```

``` AWS CLI Example
 
aws dynamodb get-item \
--table-name Users \
--key '{"userId": {"S": "123"}}' \
--consistent-read  #  Enables strong consistency

```

```️ Terraform Example: Creating a DynamoDB Table
Terraform does not set consistency itself—read consistency is a runtime behavior, not a table-level setting. But here’s how to define a table:

resource "aws_dynamodb_table" "users" {
name           = "Users"
billing_mode   = "PAY_PER_REQUEST"
hash_key       = "userId"

attribute {
name = "userId"
type = "S"
}

tags = {
Environment = "dev"
}
}

To tune consistency, use application-side code as shown above.
```

### DynamoDB Tradeoff Summary
| Scenario	                                                      | Recommended Read Mode         | 
|----------------------------------------------------------------|-------------------------------|
| User profile/settings	| Strongly consistent (true)    |
| Feed browsing, search, analytics | Eventually consistent (false) |
| Real-time  financial or inventory	                             | Strongly consistent           |
| Low-latency, high-throughput reads                             | 	Eventually consistent        | 

###  DynamoDB + DynamoDB Accelerator (DAX)

While DynamoDB alone does not offer native causal consistency, DAX adds this capability:

DAX = DynamoDB Accelerator is an in-memory cache for DynamoDB that supports microsecond response times.
Ensures causal consistency by guaranteeing that writes by a client will be visible to subsequent reads by the same client.

|Feature	|   DynamoDB	| DynamoDB + DAX |
|-------------------|-----------------------|-----------------------------|
|Default Consistency	| Eventually Consistent	| Causal Consistency |
|Strong Read Option	| Yes (via ConsistentRead=True)	| No (DAX doesn’t support strong reads) |
|Causal Consistency	| Not supported	| Yes (within the same client session)|


##  Special Focus:  For Leader/Follower Systems:
In systems like Redis, Kafka, or ElasticSearch, topology (leader/follower) and client read preference can be configured to control consistency.

Examples:
Kafka: Use acks=all for strong consistency, acks=0 for fast/weak writes

MongoDB: Use readPreference=primary (strong) vs. secondaryPreferred (eventual)