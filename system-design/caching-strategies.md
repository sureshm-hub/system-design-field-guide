# five core caching strategies

## 1. Cache-Aside (Lazy Loading)
“Load only what you need, when you need it.”

This is the most popular caching strategy used by Netflix, Uber, and Twitter.

### Flow:
App checks cache for data.
If found, return from cache.
If not found, query DB → update cache → return.

```java
User user = cache.get("user:101");
if (user == null) {
    user = db.query("SELECT * FROM users WHERE id=101");
    cache.put("user:101", user);
}
return user;
```

### Pros:
Simple & effective.
Only caches what’s needed.
Great for read-heavy systems.

### Cons:
First request is a cache miss.
Stale data unless invalidated properly.

### Best for:
> APIs where reads dominate writes (e.g., dashboards, catalogs).


## 2. Read-Through Cache

## 3. Write-Through Cache

## 4. Write-Behind (Write-Back)

## 5. Refresh-Ahead Cache

## The Two-Level Cache (Netflix/Uber Pattern)

## Redis Deep Dive: Why It’s So Fast (Even Single-Threaded)

- In-memory data store: Redis keeps everything in RAM, not on disk.
  Memory access (≈ nanoseconds) is thousands of times faster than disk I/O (≈ milliseconds).

- Event-driven I/O (epoll):

- No locks or context switching

- Efficient protocol (RESP) 

- Pipelining

- jemalloc Memory Allocator

- Marshalling vs Unmarshalling — The Hidden Cost

## Example: Spring Boot with Lettuce (Async Redis Client)

## Why Redis Dominates Caching

## Summary: Choosing the Right Strategy

https://medium.com/@vishal29saraswat/mastering-caching-strategies-and-redis-internals-a-complete-guide-for-backend-engineers-648bd58f809d

