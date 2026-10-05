**Design Decision Layer:**
- Architectural Change → what you introduced
- Why It Helps → what bottleneck it removes
- New Tradeoff / Risk → what new problems you created

**How to think about it:**
- Think: **Problem → Solution → Side Effects**
- At each scale jump, we identify the dominant bottleneck and introduce a 
targeted architectural change, accepting new tradeoffs.

**Key:**
- The key is not **over-engineering early** - each layer is introduced only when 
metrics like QPS, latency, or DB saturation justify it.

| Tier-Scale | Users/Day | Latency (p95) | Infra Cost | Tuning / Performance Tips | Primary Bottleneck | Architectural Change | Why It Helps | Failure Modes | Tradeoffs |
|------------|----------:|---------------|------------|---------------------------|--------------------|----------------------|--------------|---------------|-----------|
| Tier 1 | ~10–1K | 50–100 ms | $ | Optimize code paths, use connection pooling, basic DB indexing | Single server CPU / DB | Single monolith (App + DB) | Simple, fast to build | Single point of failure | No scalability, downtime risk |
| Tier 2 | ~1K–10K | 80–150 ms | $ | Normalize schema, optimize queries, add DB indexes, tune JVM/threads | DB contention | Split App and DB | Isolates compute and storage | DB overload | Network latency between tiers |
| Tier 3 | ~10K–100K | 100–200 ms | $$ | Enable autoscaling, optimize thread pools, reduce synchronous calls, use connection pooling | App server overload | Add Load Balancer + multiple app servers | Horizontal scaling of app tier | LB failure, uneven load | Session handling complexity |
| Tier 4 | ~100K–1M | 100–300 ms | $$ | Add DB indexes, optimize read queries, tune query plans, connection pool sizing | DB read saturation | Add DB Read Replicas | Scales read throughput | Replica lag, stale reads | Eventual consistency |
| Tier 5 | ~1M–10M | 50–150 ms | $$$ | Cache hot keys, set TTLs, use cache-aside pattern, prevent cache stampede (locking/batching) | DB + latency | Add Cache (Redis/Memcached) | Reduces DB load, faster reads | Cache stampede, eviction | Cache invalidation complexity |
| Tier 6 | ~10M–50M | 50–120 ms (global) | $$$ | Optimize static assets (compression, minification), cache headers, edge caching strategies | Static content delivery | Add CDN | Offloads static traffic, reduces latency | Cache miss, CDN outage | Cache consistency, cost |
| Tier 7 | ~50M–100M | 80–150 ms | $$$ | Externalize session state, optimize serialization, reduce session size, use sticky sessions cautiously | Session/state bottleneck | Stateless app + shared session store | Enables full horizontal scaling | Session store failure | Extra network hop |
| Tier 8 | ~100M+ | 100–300 ms | $$$$ | Tune queue partitions, batch processing, idempotency, backpressure handling | Long-running tasks | Add Message Queue + Workers | Async processing, smooth spikes | Queue backlog, worker failure | Eventual processing delay |
| Tier 9 | 100M–1B+ | 100–300 ms | $$$$ | Choose good shard key, rebalance shards, optimize cross-shard queries, use consistent hashing | DB write + global scale | Sharding + Distributed Systems | Scales writes + global availability | Data inconsistency, shard imbalance | Complex ops, rebalancing |