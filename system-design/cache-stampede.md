# Cache-Stampede 
- A very popular cached item expires, many users request it at the same time. 
Since the cache is empty, all requests go to the database or backend to 
rebuild the same data. 
- That sudden spike can overload or crash the database.
- also called the **Thundering Herd** problem.

The three fixes:

| Fix                    | Meaning                                                                                   | Best For                                     | Weakness                                      |
| ---------------------- |-------------------------------------------------------------------------------------------| -------------------------------------------- | --------------------------------------------- |
| **Lock and rebuild**   | Only one request rebuilds the cache; others wait                                          | Simple protection for one hot key            | Waiting users may see latency                 |
| **Staggered expiry**   | Add random TTL jitter so many keys do not expire together and cause **database storming** | Prevents many keys expiring at the same time | Does not fully solve one extremely hot key    |
| **Background refresh** | Refresh cache before it expires                                                           | Very high-traffic, predictable hot content   | More infrastructure and scheduling complexity |


For homepage feed for 50 million users, I would pick:

> Background refresh + staggered expiry + fallback stale cache.


Why:

The homepage feed is too critical to let expire. If it expires and millions of
users hit at once, the backend will get crushed. So I would refresh it in 
the background before expiry.

A strong interview answer:

> For a homepage feed at 50 million users, I would not rely only on request-time 
rebuild. I would use background refresh for the hottest feed entries so the 
cache is always warm. 
> I would also add TTL jitter to avoid synchronized expiration across keys and 
keep a stale copy as fallback. If refresh fails, users can still see slightly 
stale data instead of causing a **database storm.**

For L5/L6 framing:

> The key idea is to decouple cache rebuild from user traffic. User requests 
should read from cache. Refresh should happen asynchronously, rate-limited, 
observable, and protected with locks so only one worker refreshes a given key. 
> This keeps latency predictable and protects the database during traffic spikes.