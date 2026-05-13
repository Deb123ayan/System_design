# Caching, Redis, and Celery: Beginner to Advanced
 
 
A comprehensive reference covering caching theory, Redis internals and patterns, Celery task queuing, and how they integrate in real-world scalable systems. Designed for use as an AI instruction document, engineering reference, and system design study guide.
 
  
## Table of Contents
 
 
1. [Caching Fundamentals](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#1-caching-fundamentals)
 
2. [Cache Design Principles](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#2-cache-design-principles)
 
3. [Caching Strategies and Patterns](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#3-caching-strategies-and-patterns)
 
4. [Cache Invalidation](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#4-cache-invalidation)
 
5. [Cache Problems and Solutions](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#5-cache-problems-and-solutions)
 
6. [Redis Introduction and Architecture](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#6-redis-introduction-and-architecture)
 
7. [Redis Data Structures](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#7-redis-data-structures)
 
8. [Redis Commands Deep Dive](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#8-redis-commands-deep-dive)
 
9. [Redis Expiration and Eviction](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#9-redis-expiration-and-eviction)
 
10. [Redis Persistence](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#10-redis-persistence)
 
11. [Redis Pub/Sub and Streams](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#11-redis-pubsub-and-streams)
 
12. [Redis Transactions and Scripting](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#12-redis-transactions-and-scripting)
 
13. [Redis Replication](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#13-redis-replication)
 
14. [Redis Sentinel](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#14-redis-sentinel)
 
15. [Redis Cluster](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#15-redis-cluster)
 
16. [Redis Security](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#16-redis-security)
 
17. [Redis as a Rate Limiter](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#17-redis-as-a-rate-limiter)
 
18. [Redis as a Session Store](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#18-redis-as-a-session-store)
 
19. [Redis as a Distributed Lock](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#19-redis-as-a-distributed-lock)
 
20. [Redis as a Leaderboard and Counter](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#20-redis-as-a-leaderboard-and-counter)
 
21. [Redis as a Message Queue](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#21-redis-as-a-message-queue)
 
22. [Redis Performance Tuning](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#22-redis-performance-tuning)
 
23. [Redis Patterns and Anti-Patterns](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#23-redis-patterns-and-anti-patterns)
 
24. [Celery Introduction and Architecture](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#24-celery-introduction-and-architecture)
 
25. [Celery Tasks](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#25-celery-tasks)
 
26. [Celery Brokers and Backends](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#26-celery-brokers-and-backends)
 
27. [Celery Configuration](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#27-celery-configuration)
 
28. [Celery Routing and Queues](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#28-celery-routing-and-queues)
 
29. [Celery Scheduling: Beat](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#29-celery-scheduling-beat)
 
30. [Celery Monitoring and Flower](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#30-celery-monitoring-and-flower)
 
31. [Celery Error Handling and Retries](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#31-celery-error-handling-and-retries)
 
32. [Celery Workflows: Chains, Groups, Chords](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#32-celery-workflows-chains-groups-chords)
 
33. [Celery with Django and FastAPI](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#33-celery-with-django-and-fastapi)
 
34. [Celery Production Patterns](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#34-celery-production-patterns)
 
35. [Redis + Celery Integration Patterns](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#35-redis--celery-integration-patterns)
 
36. [Observability and Debugging](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#36-observability-and-debugging)
 
37. [Advanced Topics](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#37-advanced-topics)
 
38. [Quick Reference Cheat Sheet](https://claude.ai/chat/d7943cd9-1a83-4ad0-92fd-3786495bbd4a#38-quick-reference-cheat-sheet)
 

  
## 1. Caching Fundamentals
 
### What is a Cache?
 
A cache is a high-speed storage layer that temporarily holds copies of frequently accessed data so future requests for that data can be served faster. The underlying principle is the **locality of reference**: data recently accessed (temporal locality) or data near recently accessed items (spatial locality) is likely to be accessed again soon.
 
### Why Cache?
 
 
- **Reduce latency**: Memory access is orders of magnitude faster than disk or network.
 
- **Reduce backend load**: Fewer reads hitting the database means the database can handle more writes or serve more concurrent users.
 
- **Reduce cost**: Fewer database reads = lower compute/IO costs, especially with cloud pay-per-use pricing.
 
- **Improve throughput**: A cached system can handle far more requests per second than one that hits the database on every request.
 
- **Absorb traffic spikes**: Cache responses remain fast even under sudden load surges.
 

 
### Cache Hit and Miss
 
**Cache Hit**: Requested data is found in cache. Served immediately from cache. No trip to the origin.
 
**Cache Miss**: Requested data is not in cache. Must fetch from the origin (database, API, disk). Then optionally store in cache for future requests.
 
**Cache Hit Ratio (Hit Rate)**: `hits / (hits + misses)`. Higher is better.
 
 
- 90%+ hit rate: Good for general use cases.
 
- 99%+ hit rate: Expected for frequently accessed static data.
 
- Low hit rate: Cache is not helping; evaluate key design and TTL.
 

 
**Cold Cache (Cold Start)**: When a cache is empty (after restart or first deployment). All requests miss until the cache warms up. Can be catastrophic on production — plan warm-up strategies.
 
### Cache Hierarchy
 
Latency increases as you move down the hierarchy:
 `L1 CPU Cache        ~1 ns        (4 KB – 64 KB) L2 CPU Cache        ~4 ns        (256 KB – 4 MB) L3 CPU Cache        ~10 ns       (4 MB – 64 MB) RAM                 ~100 ns      (GBs) In-process cache    ~100 ns–1 µs  (process heap) Distributed cache   ~0.1–5 ms    (Redis, Memcached) SSD                 ~0.1–1 ms    (GBs – TBs) HDD                 ~10–20 ms    (TBs) Network (same DC)   ~0.5 ms Network (cross-DC)  ~100+ ms `  
## 2. Cache Design Principles
 
### What to Cache
 
Good candidates:
 
 
- **Expensive computations**: Results of heavy aggregations, ML model inference, report generation.
 
- **Frequently read, rarely written data**: Product catalog, user profile, configuration, reference data.
 
- **Session state**: User sessions (auth tokens, cart contents).
 
- **API responses**: Third-party API responses (weather, stock prices) with appropriate TTLs.
 
- **Rendered views**: Pre-rendered HTML fragments or full pages.
 
- **Database query results**: Results of slow or frequent queries.
 

 
Poor candidates:
 
 
- **Highly personalized data**: Unique to each user, low reuse.
 
- **Rapidly changing data**: Data changes faster than TTL; cache always stale.
 
- **Security-sensitive data**: Credentials, payment info, PII (be very careful with encryption and access control).
 
- **Write-heavy data**: If writes dominate reads, the overhead of maintaining the cache may not be worth it.
 

 
### Cache Key Design
 
A cache key uniquely identifies a cached value. Good key design is critical.
 
**Rules for cache keys:**
 
 
- **Deterministic**: Same inputs always produce the same key.
 
- **Descriptive**: Key conveys what it stores (`user:42:profile`, not `u42p`).
 
- **Consistent**: Use a naming convention across the codebase.
 
- **Avoid collisions**: Keys must be globally unique within a cache namespace.
 

 
**Naming convention:**
 `{entity}:{id}:{attribute} user:42:profile product:sku-1234:details order:user:42:recent:limit:10 ` 
**Key prefixing for namespacing:** When multiple applications share one Redis instance, use prefixes:
 `app1:user:42:profile app2:user:42:settings ` 
**Versioned keys:** When the schema of a cached object changes, use versioned keys to avoid serving stale data with the wrong schema:
 `v2:user:42:profile ` 
**Dynamic key generation (Python example):**
 `def user_cache_key(user_id: int, fields: list[str] | None = None) -> str:     if fields:         field_str = ":".join(sorted(fields))         return f"v1:user:{user_id}:fields:{field_str}"     return f"v1:user:{user_id}:full" ` 
### Cache Granularity
 
**Fine-grained caching**: Cache individual fields (`user:42:email`).
 
 
- Pros: Precise invalidation; only update what changed.
 
- Cons: More cache entries; more round-trips to assemble a response.
 

 
**Coarse-grained caching**: Cache entire objects (`user:42:profile` → full JSON blob).
 
 
- Pros: One cache hit retrieves everything; simpler code.
 
- Cons: Any field change invalidates the whole object; more memory per entry.
 

 
Choose based on your read pattern: if you always need the full object, cache the full object.
 
### Cache Size and Memory Planning
 
Redis stores everything in RAM. Plan capacity:
 `Memory per key ≈ key_overhead + value_size + Redis_overhead Redis overhead per key ≈ ~90 bytes (for small keys) Typical small object (JSON, 200 bytes) ≈ 290–400 bytes per entry  1 million cached user profiles × 400 bytes ≈ 400 MB ` 
Use `OBJECT ENCODING key` in Redis to inspect how a value is stored. Redis uses memory-efficient encodings (listpack, ziplist, intset) for small collections.
  
## 3. Caching Strategies and Patterns
 
### Cache-Aside (Lazy Loading)
 
The most common pattern. The application manages the cache explicitly.
 `Read:   1. Check cache for key   2. Cache HIT → return value   3. Cache MISS → query database   4. Store result in cache with TTL   5. Return value  Write:   1. Update database   2. Invalidate (delete) cache entry   ← or update it ` 
**Python example:**
 `import json import redis  r = redis.Redis(host='localhost', port=6379, decode_responses=True)  def get_user(user_id: int) -> dict:     cache_key = f"user:{user_id}:profile"          # Try cache first     cached = r.get(cache_key)     if cached:         return json.loads(cached)          # Cache miss — fetch from DB     user = db.query("SELECT * FROM users WHERE id = %s", user_id)          # Store in cache for 1 hour     r.setex(cache_key, 3600, json.dumps(user))     return user  def update_user(user_id: int, data: dict) -> None:     db.execute("UPDATE users SET ... WHERE id = %s", user_id)     # Invalidate cache     r.delete(f"user:{user_id}:profile") ` 
**Pros:**
 
 
- Only requested data is cached (no wasted memory).
 
- Cache failure is graceful (falls back to DB).
 
- Works well for read-heavy workloads.
 

 
**Cons:**
 
 
- First request always misses (cold start penalty).
 
- Potential for stale data between write and invalidation.
 
- Three trips on a miss (cache, DB, cache write).
 

 
### Write-Through
 
Every write goes to the cache and the database simultaneously.
 `Write:   1. Write to cache   2. Write to database (synchronously)  Read:   1. Check cache   2. HIT → return (no miss expected if all writes go through cache)   3. MISS → fetch from DB, populate cache ` 
**Python example:**
 `def update_user(user_id: int, data: dict) -> None:     cache_key = f"user:{user_id}:profile"          # Write to DB     db.execute("UPDATE users SET ... WHERE id = %s", user_id)          # Update cache immediately     r.setex(cache_key, 3600, json.dumps(data)) ` 
**Pros:**
 
 
- Cache is always consistent with the database.
 
- No stale data issues.
 

 
**Cons:**
 
 
- Write latency is higher (must complete both cache and DB write).
 
- Cache fills with data that may never be read again.
 
- Requires careful handling if cache write fails (use transactions or rollback).
 

 
### Write-Behind (Write-Back)
 
Writes go to the cache first; the database is updated asynchronously.
 `Write:   1. Write to cache immediately   2. Mark entry as "dirty"   3. Async job flushes dirty entries to DB later  Read:   1. Always read from cache (DB may be behind) ` 
**Pros:**
 
 
- Extremely low write latency.
 
- Batch DB writes (more efficient for write-heavy systems).
 

 
**Cons:**
 
 
- Risk of data loss if cache crashes before DB write.
 
- Complex consistency management.
 
- Hard to implement correctly.
 
- Not recommended for critical data without persistent cache (Redis AOF).
 

 
### Read-Through
 
The cache sits between the application and the database. On a miss, the cache itself fetches from the database.
 
The application only ever talks to the cache.
 
**Pros:**
 
 
- Simpler application code (no cache-miss handling logic).
 

 
**Cons:**
 
 
- Cache vendor must support this (some do, e.g., AWS ElastiCache with write-through).
 
- Cache miss is still slow.
 

 
### Refresh-Ahead (Proactive Expiry)
 
Cache proactively refreshes entries before they expire, based on predicted future access.
 `On cache read:   If TTL < threshold (e.g., < 20% remaining):     → Schedule background refresh   Return current (possibly slightly stale) value ` 
**Pros:**
 
 
- Very low latency (cache always warm for hot data).
 
- No cold start for popular items.
 

 
**Cons:**
 
 
- Wastes resources refreshing data that may not be accessed again.
 
- Requires predicting what data will be popular.
 

  
## 4. Cache Invalidation
 
"There are only two hard things in Computer Science: cache invalidation and naming things." — Phil Karlton
 
Cache invalidation is the process of removing or updating cached data when the underlying data changes.
 
### TTL-Based Expiration
 
Set a time-to-live on every cached item. After TTL, the item is evicted.
 `r.setex("user:42:profile", 3600, json.dumps(user))  # expires in 1 hour ` 
**Pros**: Simple. No explicit invalidation logic. **Cons**: Serves stale data for up to TTL after the underlying data changes. Choosing the right TTL requires knowing your acceptable staleness.
 
**TTL Guidelines:**
 
  
 
Data Type
 
Suggested TTL
 
   
 
User session
 
30 minutes – 24 hours
 
 
 
User profile
 
5–60 minutes
 
 
 
Product catalog
 
1–24 hours
 
 
 
API rate limit counters
 
1 second – 1 minute
 
 
 
Search results
 
30–300 seconds
 
 
 
Config/feature flags
 
60–300 seconds
 
 
 
Static reference data
 
1 hour – 1 day
 
 
 
Auth tokens
 
Match token expiry
 
  
 
### Event-Driven Invalidation
 
When data changes in the database, publish an event → cache layer listens and invalidates.
 
**Using Redis Pub/Sub for invalidation:**
 `# Publisher (when data changes) def update_user(user_id: int, data: dict) -> None:     db.execute("UPDATE users SET ... WHERE id = %s", user_id)     r.publish("cache:invalidate:user", str(user_id))  # Subscriber (cache invalidation service) pubsub = r.pubsub() pubsub.subscribe("cache:invalidate:user") for message in pubsub.listen():     if message['type'] == 'message':         user_id = message['data']         r.delete(f"user:{user_id}:profile") ` 
**Using database CDC (Change Data Capture):** Tools like Debezium listen to the database transaction log (binlog for MySQL, WAL for PostgreSQL) and publish change events to Kafka. Cache consumers subscribe and invalidate accordingly. Zero-latency invalidation without changing application code.
 
### Tag-Based Invalidation
 
Group related cache entries under tags. Invalidating a tag invalidates all entries with that tag.
 `# When caching, associate the key with tags def cache_with_tags(key: str, value: str, tags: list[str], ttl: int):     pipe = r.pipeline()     pipe.setex(key, ttl, value)     for tag in tags:         pipe.sadd(f"tag:{tag}", key)         pipe.expire(f"tag:{tag}", ttl + 60)  # slightly longer TTL     pipe.execute()  # Invalidate all keys with a tag def invalidate_tag(tag: str):     tag_key = f"tag:{tag}"     keys = r.smembers(tag_key)     if keys:         pipe = r.pipeline()         for key in keys:             pipe.delete(key)         pipe.delete(tag_key)         pipe.execute()  # Usage cache_with_tags("user:42:profile", user_json, tags=["user:42"], ttl=3600) cache_with_tags("user:42:orders", orders_json, tags=["user:42", "orders"], ttl=3600)  # Later, when user 42's data changes: invalidate_tag("user:42")  # deletes user:42:profile AND user:42:orders ` 
### Versioned Cache Keys
 
Instead of invalidating, increment a version number. Old keys become unreachable.
 `def get_user_cache_version(user_id: int) -> int:     version = r.get(f"user:{user_id}:version")     return int(version) if version else 1  def get_user(user_id: int) -> dict:     version = get_user_cache_version(user_id)     cache_key = f"user:{user_id}:v{version}:profile"          cached = r.get(cache_key)     if cached:         return json.loads(cached)          user = db.fetch_user(user_id)     r.setex(cache_key, 3600, json.dumps(user))     return user  def update_user(user_id: int, data: dict) -> None:     db.update_user(user_id, data)     r.incr(f"user:{user_id}:version")  # atomically bumps version     # Old versioned keys naturally expire via TTL `  
## 5. Cache Problems and Solutions
 
### Cache Stampede (Thundering Herd)
 
**Problem**: A popular cache entry expires. Simultaneously, thousands of requests miss and all query the database, overwhelming it.
 
**Solution 1 — Mutex/Lock (Redis SET NX):** Only one request populates the cache; others wait.
 `import time  def get_user_with_lock(user_id: int) -> dict:     cache_key = f"user:{user_id}:profile"     lock_key = f"lock:user:{user_id}:profile"          cached = r.get(cache_key)     if cached:         return json.loads(cached)          # Try to acquire lock (SET if Not eXists, expires in 10s)     acquired = r.set(lock_key, "1", nx=True, ex=10)          if acquired:         try:             user = db.fetch_user(user_id)             r.setex(cache_key, 3600, json.dumps(user))             return user         finally:             r.delete(lock_key)     else:         # Another process is populating; wait briefly and retry         time.sleep(0.1)         return get_user_with_lock(user_id)  # retry ` 
**Solution 2 — Probabilistic Early Expiration (XFetch):** Randomly expire entries slightly before TTL to spread cache refreshes.
 `import math import random import time  def get_with_early_expiry(key: str, beta: float = 1.0):     """     XFetch algorithm: recompute before expiry with increasing probability.     beta > 1 = more aggressive early recomputation.     """     data = r.get(key)     ttl = r.ttl(key)          if data is None:         return None, 0  # expired, must recompute          value, delta, expiry = json.loads(data)  # delta = last recompute time in seconds          # Probabilistically decide to recompute early     if -delta * beta * math.log(random.random()) >= ttl:         return None, delta  # trigger early recompute          return value, delta ` 
**Solution 3 — Stale-While-Revalidate:** Serve the stale value immediately, refresh asynchronously.
 `def get_user_stale_revalidate(user_id: int) -> dict:     cache_key = f"user:{user_id}:profile"     stale_key = f"stale:user:{user_id}:profile"  # longer TTL backup          fresh = r.get(cache_key)     if fresh:         return json.loads(fresh)          # Serve stale while refreshing     stale = r.get(stale_key)     if stale:         # Async refresh (fire and forget)         refresh_user_async.delay(user_id)  # Celery task         return json.loads(stale)          # Nothing in cache at all — synchronous fetch     user = db.fetch_user(user_id)     r.setex(cache_key, 300, json.dumps(user))     # fresh: 5 min     r.setex(stale_key, 3600, json.dumps(user))    # stale backup: 1 hour     return user ` 
### Cache Penetration
 
**Problem**: Requests for keys that don't exist in the cache OR the database (e.g., querying `user:999999` for a non-existent user). Every request misses cache and hits the DB.
 
**Solution 1 — Cache Null Values:**
 `def get_user(user_id: int) -> dict | None:     cache_key = f"user:{user_id}:profile"     cached = r.get(cache_key)          if cached is not None:         return None if cached == "NULL" else json.loads(cached)          user = db.fetch_user(user_id)     if user is None:         r.setex(cache_key, 60, "NULL")  # cache the miss for 60s     else:         r.setex(cache_key, 3600, json.dumps(user))     return user ` 
**Solution 2 — Bloom Filter:** A probabilistic data structure that tells you if a key definitely does NOT exist (no false negatives) with a small false positive rate. Check the bloom filter before hitting the cache or DB.
 `# Using redis-py-bloom or RedisBloom module # bf.add key item / bf.exists key item  def get_user(user_id: int) -> dict | None:     # Check bloom filter first (very fast)     if not r.execute_command('BF.EXISTS', 'users:bloom', user_id):         return None  # definitely doesn't exist          # Proceed with normal cache-aside logic     cache_key = f"user:{user_id}:profile"     ... ` 
### Cache Avalanche
 
**Problem**: Many cache entries expire at the same time (e.g., all set with the same TTL after a deployment), causing a surge of DB requests.
 
**Solution 1 — TTL Jitter:** Add randomness to TTL values so expirations are spread over time.
 `import random  BASE_TTL = 3600  # 1 hour JITTER = 600     # ±10 minutes  def set_with_jitter(key: str, value: str):     ttl = BASE_TTL + random.randint(-JITTER, JITTER)     r.setex(key, ttl, value) ` 
**Solution 2 — Tiered TTLs:** Use different TTL tiers for different data types. Prevents all data expiring simultaneously.
 
**Solution 3 — Circuit Breaker:** When DB error rate spikes (indicating avalanche), stop sending DB requests and serve stale cache or fallback response.
 
### Hot Key Problem
 
**Problem**: One cache key receives an extreme number of requests (e.g., a celebrity's profile). A single Redis node handles all reads for that key, becoming a bottleneck.
 
**Solution 1 — Local In-Process Cache Layer:**
 `from functools import lru_cache import time  # Small in-memory cache for the hottest keys (per-process) _local_cache: dict[str, tuple[any, float]] = {} LOCAL_TTL = 5  # seconds  def get_hot_user(user_id: int) -> dict:     local_key = f"user:{user_id}"          # Check local process cache first     if local_key in _local_cache:         value, exp = _local_cache[local_key]         if time.time() < exp:             return value          # Fall back to Redis     cache_key = f"user:{user_id}:profile"     cached = r.get(cache_key)     if cached:         value = json.loads(cached)         _local_cache[local_key] = (value, time.time() + LOCAL_TTL)         return value          # Fall back to DB     ... ` 
**Solution 2 — Key Replication (Read Replicas):** Store the same hot key across multiple Redis nodes using a shard suffix:
 `import random  def get_hot_key(base_key: str, num_replicas: int = 10) -> str:     return r.get(f"{base_key}:{random.randint(0, num_replicas - 1)}")  def set_hot_key(base_key: str, value: str, ttl: int, num_replicas: int = 10):     pipe = r.pipeline()     for i in range(num_replicas):         pipe.setex(f"{base_key}:{i}", ttl, value)     pipe.execute() `  
## 6. Redis Introduction and Architecture
 
### What is Redis?
 
Redis (Remote Dictionary Server) is an open-source, in-memory data structure store used as a database, cache, message broker, and streaming engine. Written in C.
 
**Key characteristics:**
 
 
- **In-memory**: All data stored in RAM → sub-millisecond latency.
 
- **Single-threaded command execution**: No race conditions or locking for individual commands. Commands are atomic.
 
- **Rich data structures**: Not just key-value; supports strings, hashes, lists, sets, sorted sets, streams, and more.
 
- **Persistence options**: RDB snapshots and AOF (Append-Only File) logs.
 
- **Replication**: Master-replica replication.
 
- **Clustering**: Horizontal scaling via Redis Cluster.
 
- **Lua scripting**: Execute server-side scripts atomically.
 
- **Pub/Sub and Streams**: Real-time messaging primitives.
 

 
### Redis vs Memcached
 
  
 
Feature
 
Redis
 
Memcached
 
   
 
Data structures
 
Rich (12+ types)
 
Only strings
 
 
 
Persistence
 
Yes (RDB, AOF)
 
No
 
 
 
Replication
 
Yes
 
No (use mcrouter)
 
 
 
Clustering
 
Built-in
 
External (client-side sharding)
 
 
 
Pub/Sub
 
Yes
 
No
 
 
 
Scripting
 
Lua
 
No
 
 
 
Sorted sets
 
Yes
 
No
 
 
 
Memory efficiency
 
Good
 
Slightly better for pure caching
 
 
 
Multithreading
 
I/O threaded (v6+)
 
Fully multithreaded
 
  
 
**Choose Redis** for almost all new projects. **Choose Memcached** only if you need pure key-value caching and want slightly better multi-core CPU utilization.
 
### Redis Architecture Internals
 
**Event Loop**: Redis uses a single-threaded event loop (similar to Node.js) powered by `ae` (async event) library. This uses `epoll` (Linux), `kqueue` (BSD/macOS), or `select`.
 
**Threading model (Redis 6+)**:
 
 
- One main thread handles command execution (still single-threaded for commands).
 
- Multiple I/O threads handle network reads/writes concurrently.
 
- Background threads handle persistence, lazy deletion.
 

 
**Memory allocation**: Redis uses `jemalloc` for efficient memory management.
 
**Data Encoding Optimization**: Redis automatically uses compact internal encodings for small collections:
 
  
 
Data Type
 
Small Encoding
 
Large Encoding
 
   
 
String
 
embstr (≤44 bytes)
 
raw
 
 
 
List
 
listpack (≤128 elements)
 
quicklist
 
 
 
Hash
 
listpack (≤128 fields)
 
hashtable
 
 
 
Set
 
listpack or intset
 
hashtable
 
 
 
Sorted Set
 
listpack (≤128 members)
 
skiplist + hashtable
 
  
 
### Redis Memory Layout
 
Each Redis object (`robj`) has:
 
 
- Type (string, list, hash, set, zset)
 
- Encoding (embstr, raw, ziplist, hashtable, etc.)
 
- LRU clock (for LRU eviction)
 
- Reference count
 
- Pointer to actual data
 

  
## 7. Redis Data Structures
 
### Strings
 
The simplest type. A key maps to a binary-safe byte string (up to 512 MB). Used for: cache values, counters, session tokens, JSON blobs, binary data.
 `SET key value [EX seconds] [PX milliseconds] [NX|XX] [KEEPTTL] GET key GETSET key value          # Set and return old value (atomic) MSET k1 v1 k2 v2          # Set multiple MGET k1 k2                # Get multiple INCR key                  # Atomic increment by 1 INCRBY key amount         # Increment by amount DECR key DECRBY key amount INCRBYFLOAT key amount APPEND key value          # Append to string STRLEN key                # Length GETRANGE key start end    # Substring SETRANGE key offset value # Overwrite at offset ` 
**Encoding details:**
 
 
- Integer values stored as `int` (up to 64-bit signed) — no serialization overhead.
 
- Short strings (≤44 bytes): `embstr` — object and string data in one allocation, fast.
 
- Long strings: `raw` — two allocations, mutable.
 

 
### Hashes
 
A map of field-value pairs within a single key. Like a row in a database table or a Python dict.
 `HSET key field value [field value ...] HGET key field HMGET key f1 f2           # Get multiple fields HGETALL key               # Get all field-value pairs HKEYS key HVALS key HDEL key field [field ...] HEXISTS key field HLEN key HINCRBY key field amount HINCRBYFLOAT key field amount HSCAN key cursor [MATCH pattern] [COUNT count] ` 
**Use cases:**
 
 
- User profiles: `HSET user:42 name Alice email alice@ex.com age 30`
 
- Configuration: `HSET app:config max_retries 3 timeout_ms 5000`
 
- Session data: `HSET session:abc123 user_id 42 role admin expires 1700000000`
 

 
**vs String with JSON**: Hashes allow updating individual fields without fetching/deserializing/reserializing the whole object. But HGETALL needs to deserialize the entire hash. For read-mostly with whole-object reads, JSON strings may be simpler.
 
### Lists
 
Ordered sequences of strings. Implemented as a doubly-linked list (or listpack for small lists). O(1) push/pop at both ends; O(N) random access.
 `LPUSH key value [value ...]    # Push to left (head) RPUSH key value [value ...]    # Push to right (tail) LPOP key [count]               # Pop from left RPOP key [count]               # Pop from right LRANGE key start stop          # Get range (0 -1 = all) LLEN key LINDEX key index               # Get element at index LSET key index value           # Set element at index LINSERT key BEFORE|AFTER pivot value LREM key count value           # Remove occurrences LTRIM key start stop           # Trim to range BLPOP key [key ...] timeout    # Blocking pop (left) BRPOP key [key ...] timeout    # Blocking pop (right) LMOVE src dst LEFT|RIGHT LEFT|RIGHT  # Atomic move ` 
**Use cases:**
 
 
- Activity feeds: `LPUSH user:42:feed "Alice posted X"` + `LRANGE user:42:feed 0 19`
 
- Recent items: Maintain a list of last N items with `LPUSH` + `LTRIM`.
 
- Simple task queue: `RPUSH queue task` + `BLPOP queue 0` (blocking pop waits for new tasks).
 
- Chat history: Append messages to a list.
 

 
### Sets
 
Unordered collection of unique strings. O(1) add, remove, membership test.
 `SADD key member [member ...] SREM key member [member ...] SISMEMBER key member          # Check membership: O(1) SMISMEMBER key m1 m2          # Check multiple members SMEMBERS key                  # Get all members SCARD key                     # Cardinality (size) SRANDMEMBER key [count]       # Random member(s) SPOP key [count]              # Remove and return random member SUNION key [key ...]          # Union SINTER key [key ...]          # Intersection SDIFF key [key ...]           # Difference SUNIONSTORE dst key [key ...] SINTERSTORE dst key [key ...] SDIFFSTORE dst key [key ...] SSCAN key cursor [MATCH pattern] [COUNT count] ` 
**Use cases:**
 
 
- Tags: `SADD article:42:tags python redis caching`
 
- Unique visitors: `SADD visitors:2024-01-01 user123 user456`
 
- Mutual friends: `SINTER user:42:friends user:99:friends`
 
- Permissions: `SADD user:42:roles admin editor`
 
- Inverted index: `SADD tag:python article:42 article:55`
 

 
### Sorted Sets (ZSets)
 
Like Sets but each member has a floating-point score. Members are unique; scores are not. Ordered by score (ascending). Implemented as a skip list + hash table.
 `ZADD key [NX|XX] [GT|LT] [CH] [INCR] score member [score member ...] ZREM key member [member ...] ZSCORE key member              # Get score ZRANK key member               # Rank (0-based, ascending) ZREVRANK key member            # Rank (descending) ZRANGE key start stop [BYSCORE|BYLEX] [REV] [LIMIT offset count] [WITHSCORES] ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count] ZRANGEBYLEX key min max [LIMIT offset count] ZREVRANGE key start stop [WITHSCORES] ZREVRANGEBYSCORE key max min [WITHSCORES] ZCOUNT key min max            # Count members in score range ZLEXCOUNT key min max ZINCRBY key increment member  # Increment score ZPOPMIN key [count]           # Pop lowest score ZPOPMAX key [count]           # Pop highest score ZCARD key                     # Cardinality ZUNIONSTORE dst numkeys key [key ...] [WEIGHTS ...] [AGGREGATE SUM|MIN|MAX] ZINTERSTORE dst numkeys key [key ...] ZDIFF key [key ...] [WITHSCORES] ZSCAN key cursor [MATCH pattern] [COUNT count] ` 
**Use cases:**
 
 
- **Leaderboards**: Score = points. `ZADD leaderboard 1500 user:42` → `ZREVRANGE leaderboard 0 9 WITHSCORES` (top 10).
 
- **Rate limiting**: Score = timestamp. Count members in a time window.
 
- **Priority queue**: Score = priority. `ZPOPMIN` always pops lowest-priority item.
 
- **Expiring set members**: Score = expiry timestamp. Remove expired members with `ZRANGEBYSCORE 0 now`.
 
- **Geo queries (approximation)**: Score = geohash. (Redis has native GEO commands that use ZSets internally.)
 
- **Range queries**: Efficiently find items in a score range.
 

 
### Bitmaps
 
Not a separate type — operations on string values treating them as bit arrays. Very memory-efficient: 1 bit per user for a 100M user bitmap = 12.5 MB.
 `SETBIT key offset value     # Set bit at offset to 0 or 1 GETBIT key offset           # Get bit at offset BITCOUNT key [start end]    # Count set bits BITOP AND|OR|XOR|NOT destkey key [key ...]  # Bitwise operations BITPOS key bit [start end]  # First set/clear bit position ` 
**Use cases:**
 
 
- **Daily active users**: `SETBIT dau:2024-01-01 user_id 1`. Count with `BITCOUNT`.
 
- **Feature flags per user**: `SETBIT feature:dark_mode user_id 1`
 
- **Attendance tracking**: Each bit = one day for a user.
 

 
### HyperLogLog
 
A probabilistic data structure for counting unique items (cardinality estimation) using a fixed amount of memory (~12 KB), regardless of the number of unique items. Error rate ≈ 0.81%.
 `PFADD key element [element ...]   # Add elements PFCOUNT key [key ...]             # Estimated unique count PFMERGE destkey sourcekey [sourcekey ...]  # Merge HLLs ` 
**Use cases:**
 
 
- Unique visitors per page per day: `PFADD visits:page:home:2024-01-01 user123`
 
- Unique searches per day
 
- When exact count isn't required and you have millions of items
 

 
### Geospatial
 
Built on sorted sets. Internally uses a 52-bit geohash as the score.
 `GEOADD key longitude latitude member [...] GEODIST key member1 member2 [m|km|mi|ft] GEOPOS key member [member ...]       # Get lon/lat GEOSEARCH key FROMMEMBER member | FROMLONLAT lon lat BYRADIUS radius unit | BYBOX w h unit [ASC|DESC] [COUNT count] GEOSEARCHSTORE dest src ...          # Store results GEOHASH key member [member ...]      # Get geohash string ` 
**Use cases:** Nearby restaurants, drivers near a location, proximity search.
 
### Streams
 
An append-only log data structure. Similar to Kafka topics but within Redis. Supports consumer groups for distributed consumption.
 `XADD key [MAXLEN [~] count] * field value [field value ...] XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...] XRANGE key start end [COUNT count] XREVRANGE key end start [COUNT count] XLEN key XACK key group id [id ...] XGROUP CREATE key group id [MKSTREAM] XREADGROUP GROUP group consumer [COUNT n] [BLOCK ms] STREAMS key >  XPENDING key group [[IDLE min-idle-time] start end count [consumer]] XCLAIM key group consumer min-idle-time id [id ...] XDEL key id [id ...] XTRIM key MAXLEN [~] count ` 
**Use cases:** Event log, activity stream, IoT sensor data, audit trail, as a lightweight Kafka replacement.
  
## 8. Redis Commands Deep Dive
 
### Key Management
 `EXISTS key [key ...]          # 1 if exists DEL key [key ...]             # Synchronous delete UNLINK key [key ...]          # Async delete (non-blocking) TYPE key                      # Returns: string|list|set|zset|hash|stream RENAME key newkey RENAMENX key newkey           # Rename only if newkey doesn't exist COPY src dst [DB n] [REPLACE] PERSIST key                   # Remove TTL EXPIRE key seconds            # Set TTL in seconds PEXPIRE key milliseconds      # Set TTL in milliseconds EXPIREAT key timestamp        # Set expiry as Unix timestamp PEXPIREAT key ms-timestamp TTL key                       # Remaining TTL in seconds (-1 = no TTL, -2 = doesn't exist) PTTL key                      # Remaining TTL in milliseconds OBJECT ENCODING key           # Internal encoding OBJECT REFCOUNT key           # Reference count OBJECT IDLETIME key           # Seconds since last access OBJECT FREQ key               # Access frequency (LFU) OBJECT HELP DUMP key                      # Serialize value RESTORE key ttl serialized-value ` 
### Scanning Keys (Never Use KEYS in Production)
 
`KEYS pattern` blocks Redis while scanning all keys. Use `SCAN` instead:
 `SCAN cursor [MATCH pattern] [COUNT count] [TYPE type] # cursor starts at 0; iterate until cursor returns to 0 ` `def scan_keys(pattern: str) -> list[str]:     keys = []     cursor = 0     while True:         cursor, batch = r.scan(cursor, match=pattern, count=100)         keys.extend(batch)         if cursor == 0:             break     return keys ` 
### Pipelines (Batching Commands)
 
Sending commands one-by-one wastes RTT. Pipelines batch multiple commands into a single network round-trip.
 `# Without pipeline: 3 round trips r.set("k1", "v1") r.set("k2", "v2") r.set("k3", "v3")  # With pipeline: 1 round trip pipe = r.pipeline(transaction=False) pipe.set("k1", "v1") pipe.set("k2", "v2") pipe.set("k3", "v3") results = pipe.execute() ` 
Pipelines are not atomic (use MULTI/EXEC for atomicity).
 
### Server Commands
 `INFO [section]             # Server statistics (all, memory, clients, stats, replication...) CONFIG GET parameter       # Get config parameter CONFIG SET parameter value # Set config at runtime CONFIG REWRITE             # Write current config to file DBSIZE                     # Number of keys in current DB FLUSHDB [ASYNC]            # Delete all keys in current DB FLUSHALL [ASYNC]           # Delete all keys in all DBs SELECT db                  # Switch database (0-15) SWAPDB db1 db2             # Atomically swap two databases BGSAVE                     # Background RDB save BGREWRITEAOF               # Background AOF rewrite LASTSAVE                   # Unix timestamp of last successful save DEBUG SLEEP seconds        # Block server (for testing) SLOWLOG GET [count]        # Recent slow queries SLOWLOG LEN SLOWLOG RESET CLIENT LIST                # List connected clients CLIENT GETNAME CLIENT SETNAME name CLIENT KILL ID client-id CLIENT INFO COMMAND COUNT              # Number of Redis commands COMMAND INFO cmd [cmd ...] LATENCY HISTORY event LATENCY LATEST MONITOR                    # Stream all commands (dangerous, high overhead) `  
## 9. Redis Expiration and Eviction
 
### Key Expiration Mechanisms
 
Redis uses two mechanisms to delete expired keys:
 
**Lazy Expiration**: When a key is accessed, Redis checks if it's expired. If so, deletes it and returns nil. This means expired keys may linger if never accessed.
 
**Active Expiration**: Redis periodically samples random keys from those with TTLs and deletes expired ones. Frequency controlled by `hz` config (default: 10 Hz = 10 times/second, adaptive in Redis 6+).
 
### Memory Eviction Policies
 
When Redis reaches `maxmemory`, it must evict keys to free space. Controlled by `maxmemory-policy` config:
 
  
 
Policy
 
Description
 
   
 
`noeviction`
 
Return error on write (default). No data loss but clients fail.
 
 
 
`allkeys-lru`
 
Evict any key using LRU. Good for general caching.
 
 
 
`volatile-lru`
 
Evict keys with TTL using LRU. Safer — never evicts non-expiring keys.
 
 
 
`allkeys-lfu`
 
Evict any key using LFU. Better for skewed access patterns.
 
 
 
`volatile-lfu`
 
Evict keys with TTL using LFU.
 
 
 
`allkeys-random`
 
Evict random key. Not recommended.
 
 
 
`volatile-random`
 
Evict random key with TTL.
 
 
 
`volatile-ttl`
 
Evict key with shortest TTL first.
 
  
 
**Recommendation**: Use `allkeys-lru` for pure caching. Use `volatile-lru` if Redis holds both ephemeral cached data and persistent data (sessions).
 
### Configuring Memory Limits
 `CONFIG SET maxmemory 4gb CONFIG SET maxmemory-policy allkeys-lru CONFIG SET maxmemory-samples 10  # LRU sample size (higher = more accurate but slower) ` 
### LRU vs LFU
 
Redis's LRU is approximate — it samples a set of keys and evicts the least recently used among the sample (not true global LRU, for performance reasons).
 
**LRU (Least Recently Used)**: Good when recency predicts future access. Fails for scanning patterns where every key is accessed once.
 
**LFU (Least Frequently Used)**: Tracks access frequency with a decay algorithm (`lfu-decay-time`, default 1 minute). Better for stable "popular data stays popular" patterns.
  
## 10. Redis Persistence
 
By default, Redis is an in-memory database. Data is lost on restart unless persistence is configured.
 
### RDB (Redis Database) Snapshots
 
Periodically writes the entire dataset to a binary `.rdb` file using a fork. The child process writes to disk while the parent continues serving requests (copy-on-write).
 
**Configuration (`redis.conf`):**
 `save 900 1       # Save if 1 key changed in 900s save 300 10      # Save if 10 keys changed in 300s save 60 10000    # Save if 10000 keys changed in 60s dbfilename dump.rdb dir /var/lib/redis ` 
**Pros:**
 
 
- Compact binary format, smaller than AOF.
 
- Faster restart (load single file vs replaying log).
 
- Good for backups and disaster recovery.
 

 
**Cons:**
 
 
- Data loss equal to the interval between snapshots.
 
- Fork can cause latency spikes on large datasets (copy-on-write memory pressure).
 

 
### AOF (Append-Only File)
 
Logs every write command to an append-only file. On restart, replays the log to reconstruct data.
 
**Configuration:**
 `appendonly yes appendfilename "appendonly.aof" appendfsync everysec   # Options: always | everysec | no ` 
**`appendfsync` options:**
 
 
- `always`: `fsync` after every command. Maximum durability, lowest performance.
 
- `everysec`: `fsync` once per second. At most 1 second of data loss. Recommended.
 
- `no`: OS decides when to flush. Fastest, potentially larger data loss.
 

 
**AOF Rewriting**: AOF grows over time (it logs every command, even overwritten values). Redis periodically rewrites the AOF (keeping only the minimal set of commands to reconstruct current state). Triggered by `BGREWRITEAOF` or automatically.
 
**Pros:**
 
 
- Much more durable than RDB (at most 1 second of loss with `everysec`).
 
- Human-readable (for debugging, though now binary format in v7+).
 

 
**Cons:**
 
 
- Larger file size than RDB.
 
- Slower restart (must replay entire log).
 

 
### Mixed Persistence (Redis 4+, Default in Redis 7)
 
RDB + AOF hybrid. AOF file begins with an RDB snapshot (fast loading) followed by the AOF diff (minimal data loss).
 `aof-use-rdb-preamble yes ` 
**Best practice for production:** Enable AOF with `appendfsync everysec` + periodic RDB backups.
 
### Disabling Persistence (Pure Cache)
 `save ""         # Disable RDB appendonly no   # Disable AOF ` 
Use when Redis is purely a cache and losing data on restart is acceptable.
  
## 11. Redis Pub/Sub and Streams
 
### Pub/Sub
 
A messaging pattern where publishers send messages to channels and subscribers receive them. Messages are not persisted — if a subscriber is offline, it misses the message.
 `# Publisher PUBLISH channel message  # Subscriber SUBSCRIBE channel [channel ...] PSUBSCRIBE pattern [pattern ...]  # Pattern subscribe (e.g., user.*) UNSUBSCRIBE [channel ...] PUNSUBSCRIBE [pattern ...] PUBSUB CHANNELS [pattern]         # List active channels PUBSUB NUMSUB [channel ...]       # Subscriber count per channel PUBSUB NUMPAT                     # Number of pattern subscriptions ` 
**Python example:**
 `import threading  def publisher():     import time     while True:         r.publish("notifications", json.dumps({"type": "alert", "msg": "hello"}))         time.sleep(1)  def subscriber():     pubsub = r.pubsub()     pubsub.subscribe("notifications")     for message in pubsub.listen():         if message['type'] == 'message':             data = json.loads(message['data'])             print(f"Received: {data}")  threading.Thread(target=subscriber, daemon=True).start() publisher() ` 
**Limitations:**
 
 
- No message persistence (fire and forget).
 
- No acknowledgment or delivery guarantee.
 
- Subscribers must be connected at the time of publish.
 

 
**Use cases:** Real-time notifications, cache invalidation events, live dashboards, chat (with external persistence for history).
 
For reliable messaging with persistence, use Redis Streams instead.
 
### Redis Streams
 
A persistent, append-only log with consumer group support. More powerful than Pub/Sub.
 
**Key concepts:**
 
 
- **Stream**: An ordered sequence of entries (key-value pairs with a timestamp-based ID).
 
- **Entry ID**: `timestamp-sequence` (e.g., `1700000000000-0`). Auto-generated with `*`.
 
- **Consumer Group**: Multiple consumers share a stream, each processing a subset.
 
- **Pending Entries List (PEL)**: Entries delivered to a consumer but not yet ACKed.
 

 `# Producer def produce_event(stream: str, data: dict) -> str:     entry_id = r.xadd(stream, data, maxlen=10000, approximate=True)     return entry_id  # Consumer (with consumer group) GROUP = "processors" CONSUMER = "worker-1" STREAM = "events"  # Create consumer group (once) try:     r.xgroup_create(STREAM, GROUP, id='0', mkstream=True) except redis.exceptions.ResponseError:     pass  # Group already exists  # Process messages while True:     messages = r.xreadgroup(GROUP, CONSUMER, {STREAM: '>'}, count=10, block=5000)     if messages:         for stream, entries in messages:             for entry_id, fields in entries:                 try:                     process(fields)                     r.xack(STREAM, GROUP, entry_id)                 except Exception as e:                     # Leave in PEL for retry                     log_error(e)  # Reclaim stale messages (from crashed consumers) pending = r.xpending_range(STREAM, GROUP, '-', '+', 100) for msg in pending:     if msg['time_since_delivered'] > 30000:  # 30 seconds         r.xclaim(STREAM, GROUP, CONSUMER, 30000, [msg['message_id']]) `  
## 12. Redis Transactions and Scripting
 
### Transactions (MULTI/EXEC)
 
MULTI/EXEC groups commands into an atomic block. All commands execute sequentially; no other client's commands can interleave.
 `MULTI         # Begin transaction SET k1 v1 INCR counter ZADD leaderboard 100 user:42 EXEC          # Execute all queued commands atomically DISCARD       # Cancel the transaction ` 
**Important**: MULTI/EXEC does NOT provide rollback. If one command fails, the others still execute. Redis transactions guarantee atomic execution (all-or-nothing in terms of isolation), not rollback-on-error.
 
### Optimistic Locking with WATCH
 
WATCH enables optimistic locking. If a watched key changes before EXEC, the transaction is aborted.
 `def transfer_credits(from_user: int, to_user: int, amount: int) -> bool:     from_key = f"user:{from_user}:credits"     to_key = f"user:{to_user}:credits"          with r.pipeline() as pipe:         while True:             try:                 pipe.watch(from_key)  # Watch for changes                 from_balance = int(pipe.get(from_key) or 0)                                  if from_balance < amount:                     pipe.unwatch()                     return False                                  pipe.multi()  # Start transaction                 pipe.decrby(from_key, amount)                 pipe.incrby(to_key, amount)                 pipe.execute()  # Will fail if from_key changed since WATCH                 return True             except redis.WatchError:                 continue  # Retry on conflict ` 
### Lua Scripting
 
Lua scripts execute atomically on the Redis server. More powerful than MULTI/EXEC because you can include conditional logic.
 `EVAL script numkeys key [key ...] arg [arg ...] EVALSHA sha1 numkeys key [key ...] arg [arg ...] SCRIPT LOAD script           # Load script, get SHA1 SCRIPT EXISTS sha1 [sha1 ...] SCRIPT FLUSH                 # Clear script cache ` 
**Atomic check-and-set example:**
 `# Decrement a counter only if it's above a threshold lua_script = """ local current = tonumber(redis.call('GET', KEYS[1])) if current == nil then     return -1 end if current >= tonumber(ARGV[1]) then     return redis.call('DECRBY', KEYS[1], ARGV[2]) else     return -2 end """  script = r.register_script(lua_script)  # Execute result = script(     keys=["user:42:credits"],     args=[10, 5]  # only deduct 5 if balance >= 10 ) ` 
**Rate limiter Lua script:**
 `rate_limit_script = """ local key = KEYS[1] local limit = tonumber(ARGV[1]) local window = tonumber(ARGV[2])  local current = redis.call('INCR', key) if current == 1 then     redis.call('EXPIRE', key, window) end if current > limit then     return 0  -- rate limited else     return 1  -- allowed end """ `  
## 13. Redis Replication
 
### How Replication Works
 
Redis replication is asynchronous by default. One primary (master) can have multiple replicas (slaves).
 
**Full Resynchronization (first connect):**
 
 
1. Replica sends `REPLICAOF host port` to primary.
 
2. Primary forks, creates RDB snapshot, sends it to replica.
 
3. While sending, primary buffers incoming write commands in the replication backlog.
 
4. After RDB is loaded, replica receives and applies buffered commands.
 
5. Ongoing: primary streams all write commands to replica in real time.
 

 
**Partial Resynchronization (reconnect after disconnect):** If the replica disconnects briefly, it sends its replication offset and replication ID. If the offset is within the primary's backlog (`repl-backlog-size`, default 1 MB), only missing commands are sent.
 
**Configuration:**
 `# On replica replicaof <masterip> <masterport> masterauth <password> replica-read-only yes        # Replicas are read-only by default replica-lazy-flush no repl-backlog-size 10mb       # Buffer for partial resync repl-timeout 60 ` 
### Replication Lag
 
Replication is asynchronous. The replica may be behind the primary by a few milliseconds or more under heavy load. Monitor with `INFO replication` → `master_repl_offset` vs `slave_repl_offset`.
 
**Minimizing replication lag:**
 
 
- Increase `repl-backlog-size` to handle disconnections.
 
- Use dedicated network for replication traffic.
 
- Monitor `repl_behind_master_seconds` metric.
 

 
### Promoting a Replica
 
If the primary fails:
 `REPLICAOF NO ONE    # Promote replica to primary ` 
Then reconfigure other replicas to point to the new primary. This is automated by Redis Sentinel.
  
## 14. Redis Sentinel
 
Redis Sentinel provides high availability through automatic failover. It monitors Redis instances and coordinates failover when the primary fails.
 
### Sentinel Architecture
 `        [Sentinel 1]        /     |      \ [Primary]  [Replica1]  [Replica2]        \     |      /         [Sentinel 2]         [Sentinel 3]   ← Minimum 3 sentinels for quorum ` 
### Sentinel Responsibilities
 
 
1. **Monitoring**: Continuously checks primary and replica health via `PING`.
 
2. **Notification**: Notifies administrators/applications when a Redis instance changes state.
 
3. **Automatic Failover**: If primary is unreachable (confirmed by quorum of sentinels), promotes a replica to primary and reconfigures others.
 
4. **Configuration Provider**: Clients query Sentinel to discover the current primary address.
 

 
### Failover Process
 
 
1. Sentinel detects primary is unreachable (SDOWN = subjectively down).
 
2. Quorum of sentinels agree → ODOWN (objectively down).
 
3. Sentinels elect a leader sentinel via Raft.
 
4. Leader sentinel selects the best replica (lowest replication lag, highest priority).
 
5. Leader sends `REPLICAOF NO ONE` to the selected replica.
 
6. Leader reconfigures other replicas to follow the new primary.
 
7. Sentinel notifies clients of the new primary address.
 

 
### Sentinel Configuration
 `# sentinel.conf sentinel monitor mymaster 127.0.0.1 6379 2    # quorum = 2 sentinel auth-pass mymaster <password> sentinel down-after-milliseconds mymaster 5000  # 5s to declare SDOWN sentinel failover-timeout mymaster 60000 sentinel parallel-syncs mymaster 1             # replicas syncing at once ` 
### Connecting via Sentinel (Python)
 `from redis.sentinel import Sentinel  sentinels = [     ('sentinel-1', 26379),     ('sentinel-2', 26379),     ('sentinel-3', 26379), ]  sentinel = Sentinel(sentinels, socket_timeout=0.1) master = sentinel.master_for('mymaster', socket_timeout=0.1, password='secret') replica = sentinel.slave_for('mymaster', socket_timeout=0.1)  master.set('key', 'value')       # writes to primary replica.get('key')               # reads from replica `  
## 15. Redis Cluster
 
Redis Cluster provides horizontal scaling across multiple nodes. Data is automatically sharded across cluster nodes.
 
### Hash Slots
 
Redis Cluster divides the keyspace into **16,384 hash slots**. Each key is assigned to a slot:
 `slot = CRC16(key) % 16384 ` 
Each master node is responsible for a subset of slots (e.g., 3 masters: 0–5460, 5461–10922, 10923–16383).
 
### Cluster Topology
 `Master1 (slots 0–5460)    ← Replica1A, Replica1B Master2 (slots 5461–10922) ← Replica2A, Replica2B Master3 (slots 10923–16383) ← Replica3A, Replica3B ` 
Minimum production cluster: 3 masters + 3 replicas = 6 nodes.
 
### Hash Tags
 
By default, only the key itself determines the slot. To ensure multiple keys land on the same slot (for multi-key commands), use hash tags — the part inside `{}` determines the slot:
 `user:{42}:profile  → slot based on "42" user:{42}:sessions → slot based on "42" # Both keys on same slot → MGET, transactions work ` 
Without hash tags, `MGET` across different slots requires client-side logic.
 
### Cluster Commands
 `CLUSTER INFO CLUSTER NODES CLUSTER SLOTS CLUSTER KEYSLOT key            # Which slot does this key map to? CLUSTER COUNTKEYSINSLOT slot CLUSTER GETKEYSINSLOT slot count CLUSTER FAILOVER [FORCE|TAKEOVER] CLUSTER RESET [HARD|SOFT] CLUSTER REPLICATE node-id      # Make this node a replica of node-id CLUSTER FLUSHSLOTS ` 
### Cluster Setup (Quick)
 `# Using redis-cli (Redis 5+) redis-cli --cluster create \   127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 \   127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \   --cluster-replicas 1  # Check cluster status redis-cli -c -p 7000 cluster info ` 
### Connecting to Redis Cluster (Python)
 `from redis.cluster import RedisCluster, ClusterNode  startup_nodes = [     ClusterNode('127.0.0.1', 7000),     ClusterNode('127.0.0.1', 7001),     ClusterNode('127.0.0.1', 7002), ]  rc = RedisCluster(startup_nodes=startup_nodes, decode_responses=True) rc.set("key", "value") ` 
### Resharding
 
When adding/removing nodes, slots must be redistributed:
 `redis-cli --cluster reshard 127.0.0.1:7000 \   --cluster-from <source-node-id> \   --cluster-to <destination-node-id> \   --cluster-slots 1000 `  
## 16. Redis Security
 
### Authentication
 `# redis.conf requirepass your_strong_password_here  # ACL (Redis 6+) — per-user permissions aclfile /etc/redis/users.acl ` 
**ACL (Access Control Lists — Redis 6+):**
 `ACL SETUSER alice on >password ~cached:* &* +get +set +del # alice can only: GET, SET, DEL keys matching "cached:*"  ACL SETUSER bob on >password ~* &* +@read   # read-only ACL WHOAMI ACL LIST ACL CAT                          # List command categories ACL CAT string                   # Commands in category "string" ACL GETUSER username ACL DELUSER username ACL SAVE                         # Save to aclfile ACL LOAD                         # Reload from aclfile ACL LOG [count | RESET]          # Show ACL violations ` 
### Network Security
 `# Bind to specific interfaces (not 0.0.0.0) bind 127.0.0.1 10.0.0.5  # Disable protected mode if binding to non-loopback protected-mode yes  # TLS (Redis 6+) tls-port 6380 tls-cert-file /path/to/redis.crt tls-key-file /path/to/redis.key tls-ca-cert-file /path/to/ca.crt tls-auth-clients yes ` 
### Security Best Practices
 
 
- Never expose Redis directly to the internet. Place behind a VPC/firewall.
 
- Use strong passwords (or ACL users with strong passwords).
 
- Disable `CONFIG`, `FLUSHALL`, `DEBUG` commands for untrusted users using `rename-command`.
 
- Use TLS for encryption in transit.
 
- Enable AOF persistence to prevent data loss from crashes.
 
- Run Redis as a non-root user.
 
- Rename dangerous commands:
 

 `# redis.conf rename-command FLUSHALL ""        # Disable completely rename-command CONFIG "CONFIG_xyz123abc"   # Rename to obscure name rename-command DEBUG "" rename-command SLAVEOF "" `  
## 17. Redis as a Rate Limiter
 
### Fixed Window Rate Limiter
 `def is_allowed_fixed(user_id: str, limit: int, window_seconds: int) -> bool:     key = f"ratelimit:{user_id}:{int(time.time()) // window_seconds}"     count = r.incr(key)     if count == 1:         r.expire(key, window_seconds)     return count <= limit ` 
### Sliding Window Rate Limiter (Sorted Set)
 `def is_allowed_sliding(user_id: str, limit: int, window_seconds: int) -> bool:     now = time.time()     window_start = now - window_seconds     key = f"ratelimit:sliding:{user_id}"          pipe = r.pipeline()     pipe.zremrangebyscore(key, 0, window_start)   # Remove old entries     pipe.zadd(key, {str(now): now})               # Add current request     pipe.zcard(key)                               # Count requests in window     pipe.expire(key, window_seconds)     results = pipe.execute()          return results[2] <= limit  # results[2] = zcard result ` 
### Token Bucket with Lua (Atomic)
 `token_bucket_script = r.register_script(""" local key = KEYS[1] local capacity = tonumber(ARGV[1]) local refill_rate = tonumber(ARGV[2])   -- tokens per second local now = tonumber(ARGV[3]) local requested = tonumber(ARGV[4])  local data = redis.call('HMGET', key, 'tokens', 'last_refill') local tokens = tonumber(data[1]) or capacity local last_refill = tonumber(data[2]) or now  -- Refill tokens based on elapsed time local elapsed = now - last_refill local new_tokens = math.min(capacity, tokens + (elapsed * refill_rate))  if new_tokens >= requested then     redis.call('HMSET', key, 'tokens', new_tokens - requested, 'last_refill', now)     redis.call('EXPIRE', key, math.ceil(capacity / refill_rate) + 1)     return 1  -- allowed else     redis.call('HMSET', key, 'tokens', new_tokens, 'last_refill', now)     redis.call('EXPIRE', key, math.ceil(capacity / refill_rate) + 1)     return 0  -- rate limited end """)  def is_allowed_token_bucket(user_id: str, capacity: int, refill_rate: float) -> bool:     result = token_bucket_script(         keys=[f"tokenbucket:{user_id}"],         args=[capacity, refill_rate, time.time(), 1]     )     return bool(result) `  
## 18. Redis as a Session Store
 
### Session Pattern
 `import uuid import json  SESSION_TTL = 1800  # 30 minutes  def create_session(user_id: int, metadata: dict) -> str:     session_id = str(uuid.uuid4())     session_data = {         "user_id": user_id,         "created_at": int(time.time()),         **metadata     }     r.setex(f"session:{session_id}", SESSION_TTL, json.dumps(session_data))     return session_id  def get_session(session_id: str) -> dict | None:     data = r.get(f"session:{session_id}")     if data is None:         return None     # Slide the session expiry window     r.expire(f"session:{session_id}", SESSION_TTL)     return json.loads(data)  def destroy_session(session_id: str) -> None:     r.delete(f"session:{session_id}")  def destroy_all_user_sessions(user_id: int) -> None:     # Requires maintaining an index of sessions per user     session_ids = r.smembers(f"user:{user_id}:sessions")     if session_ids:         pipe = r.pipeline()         for sid in session_ids:             pipe.delete(f"session:{sid}")         pipe.delete(f"user:{user_id}:sessions")         pipe.execute() ` 
### Session with Hash (for field-level access)
 `def set_session_field(session_id: str, field: str, value: str) -> None:     key = f"session:{session_id}"     r.hset(key, field, value)     r.expire(key, SESSION_TTL)  def get_session_field(session_id: str, field: str) -> str | None:     return r.hget(f"session:{session_id}", field) `  
## 19. Redis as a Distributed Lock
 
A distributed lock ensures only one process executes a critical section at a time across multiple instances.
 
### Simple Lock (SET NX EX)
 `import uuid  def acquire_lock(resource: str, ttl_seconds: int) -> str | None:     lock_id = str(uuid.uuid4())  # Unique value to identify this lock holder     key = f"lock:{resource}"     acquired = r.set(key, lock_id, nx=True, ex=ttl_seconds)     return lock_id if acquired else None  def release_lock(resource: str, lock_id: str) -> bool:     # Atomic: only release if we own the lock     release_script = """     if redis.call('GET', KEYS[1]) == ARGV[1] then         return redis.call('DEL', KEYS[1])     else         return 0     end     """     key = f"lock:{resource}"     result = r.eval(release_script, 1, key, lock_id)     return bool(result)  # Context manager from contextlib import contextmanager  @contextmanager def distributed_lock(resource: str, ttl: int = 30):     lock_id = acquire_lock(resource, ttl)     if lock_id is None:         raise RuntimeError(f"Could not acquire lock for {resource}")     try:         yield lock_id     finally:         release_lock(resource, lock_id)  # Usage with distributed_lock("payment:user:42"):     process_payment(user_id=42) ` 
### Redlock (Multi-Node Lock)
 
For stronger guarantees across a Redis Cluster or multiple independent Redis nodes.
 `# Using redlock-py library from redlock import Redlock  dlm = Redlock([     {"host": "redis-1", "port": 6379},     {"host": "redis-2", "port": 6379},     {"host": "redis-3", "port": 6379}, ])  my_lock = dlm.lock("resource_name", 10000)  # 10 second TTL if my_lock:     try:         critical_section()     finally:         dlm.unlock(my_lock) ` 
**Redlock algorithm**: Acquire lock on N/2+1 Redis nodes. Valid only if acquired on quorum within the TTL window. Provides stronger guarantees than single-node lock.
 
**Controversy**: Martin Kleppmann raised concerns about Redlock's safety under clock skew and process pauses. For most use cases, single-node lock + idempotent operations is sufficient.
  
## 20. Redis as a Leaderboard and Counter
 
### Real-Time Leaderboard
 `LEADERBOARD_KEY = "game:leaderboard"  def add_score(user_id: int, score: float) -> None:     r.zadd(LEADERBOARD_KEY, {f"user:{user_id}": score})  def increment_score(user_id: int, amount: float) -> float:     return r.zincrby(LEADERBOARD_KEY, amount, f"user:{user_id}")  def get_rank(user_id: int) -> int:     """0-based rank (0 = highest score)"""     return r.zrevrank(LEADERBOARD_KEY, f"user:{user_id}")  def get_top_n(n: int) -> list[tuple[str, float]]:     return r.zrevrange(LEADERBOARD_KEY, 0, n - 1, withscores=True)  def get_user_score(user_id: int) -> float | None:     return r.zscore(LEADERBOARD_KEY, f"user:{user_id}")  def get_players_around(user_id: int, window: int = 5):     rank = get_rank(user_id)     if rank is None:         return []     start = max(0, rank - window)     end = rank + window     return r.zrevrange(LEADERBOARD_KEY, start, end, withscores=True) ` 
### Atomic Counters
 `# Page view counter r.incr("views:article:42") r.incrby("views:article:42", 5)  # Store counts by period import datetime  def record_view(article_id: int) -> None:     today = datetime.date.today().isoformat()  # "2024-01-15"     pipe = r.pipeline()     pipe.incr(f"views:{article_id}:total")     pipe.incr(f"views:{article_id}:{today}")     pipe.expire(f"views:{article_id}:{today}", 86400 * 30)  # keep 30 days     pipe.execute()  def get_views_today(article_id: int) -> int:     today = datetime.date.today().isoformat()     return int(r.get(f"views:{article_id}:{today}") or 0) `  
## 21. Redis as a Message Queue
 
### Simple Queue with Lists
 `QUEUE_KEY = "tasks:queue"  def enqueue(task: dict) -> None:     r.rpush(QUEUE_KEY, json.dumps(task))  def dequeue(timeout: int = 0) -> dict | None:     result = r.blpop(QUEUE_KEY, timeout=timeout)     if result:         _, data = result         return json.loads(data)     return None  # Worker while True:     task = dequeue(timeout=5)  # blocks for 5 seconds     if task:         process_task(task) ` 
### Reliable Queue with Acknowledgment
 `QUEUE_KEY = "tasks:queue" PROCESSING_KEY = "tasks:processing"  def enqueue_reliable(task: dict) -> None:     r.rpush(QUEUE_KEY, json.dumps(task))  def dequeue_reliable() -> tuple[str, dict] | None:     # Atomically move from queue to processing list     raw = r.lmove(QUEUE_KEY, PROCESSING_KEY, 'LEFT', 'RIGHT')     if raw:         return raw, json.loads(raw)     return None  def ack(raw_task: str) -> None:     r.lrem(PROCESSING_KEY, 1, raw_task)  def nack(raw_task: str) -> None:     # Move back to queue for retry     r.lmove(PROCESSING_KEY, QUEUE_KEY, 'RIGHT', 'LEFT')  # Recovery: re-queue tasks stuck in processing def recover_stale_tasks() -> None:     stuck = r.lrange(PROCESSING_KEY, 0, -1)     for task in stuck:         r.lmove(PROCESSING_KEY, QUEUE_KEY, 'RIGHT', 'LEFT') ` 
### Priority Queue with Sorted Set
 `def enqueue_priority(task: dict, priority: int) -> None:     r.zadd("priority:queue", {json.dumps(task): priority})  def dequeue_priority() -> dict | None:     # Pop highest priority (lowest score = highest priority)     result = r.zpopmin("priority:queue", 1)     if result:         data, score = result[0]         return json.loads(data)     return None `  
## 22. Redis Performance Tuning
 
### Benchmarking
 `# Built-in benchmark redis-benchmark -h localhost -p 6379 -c 50 -n 100000 # -c: concurrent clients # -n: total requests  # Benchmark specific commands redis-benchmark -t set,get -d 100 -n 1000000 # -d: data size in bytes # -t: test types  # Latency measurement redis-cli --latency redis-cli --latency-history -i 1  # Sample every 1s redis-cli --latency-dist           # Latency distribution chart ` 
### Key Performance Configurations
 `# redis.conf hz 20                        # Event loop frequency (default 10, max 500) dynamic-hz yes               # Adaptive hz based on connected clients  # Network tcp-backlog 511              # TCP listen backlog tcp-keepalive 300            # TCP keepalive in seconds timeout 0                    # Client timeout (0 = disabled)  # Slow log (commands slower than threshold) slowlog-log-slower-than 10000  # microseconds (10ms) slowlog-max-len 256  # Threading (Redis 6+) io-threads 4                 # I/O threads (match CPU cores, not more) io-threads-do-reads yes      # Multi-threaded reads  # Memory activerehashing yes          # Rehash hash tables when idle lazyfree-lazy-eviction yes   # Async eviction lazyfree-lazy-expire yes     # Async TTL expiration lazyfree-lazy-server-del yes # Async DEL (use UNLINK instead)  # Disable transparent huge pages (THP causes latency spikes) # Set in OS: echo never > /sys/kernel/mm/transparent_hugepage/enabled ` 
### Slow Log Analysis
 `SLOWLOG GET 10      # Last 10 slow commands SLOWLOG LEN         # Total entries in slow log SLOWLOG RESET       # Clear slow log ` 
Each entry: `[id, timestamp, execution_time_µs, [command, args...], client_addr, client_name]`
 
### Memory Analysis
 `INFO memory              # Memory stats MEMORY DOCTOR            # Suggestions for memory improvement MEMORY STATS             # Detailed memory breakdown MEMORY USAGE key [SAMPLES count]   # Memory used by one key OBJECT ENCODING key      # Internal encoding DEBUG JMAP               # Memory profiling (expensive, dev only) ` 
### Common Performance Anti-Patterns
 
 
- **`KEYS *` in production**: Blocks Redis. Use `SCAN`.
 
- **Storing large objects**: Keep values under ~1 MB. Large values slow serialization.
 
- **No connection pooling**: Reconnecting for each operation adds massive latency overhead.
 
- **Synchronous operations in hot path**: Use pipelining for batches.
 
- **No TTLs on cached data**: Memory grows unbounded.
 
- **Too many small keys**: Overhead per key is ~90 bytes; prefer hashes for many small fields.
 
- **Missing hash tags in cluster**: Multi-key operations fail across slots.
 
- **Big `O(N)` commands on large sets**: `SMEMBERS`, `HGETALL`, `LRANGE 0 -1` block Redis if N is large. Use `SSCAN`, `HSCAN`, `LRANGE` with pagination.
 

  
## 23. Redis Patterns and Anti-Patterns
 
### Patterns
 
**Atomic operations for consistency**: Use INCR, HINCRBY, ZADD, Lua scripts instead of GET + business logic + SET.
 
**Namespace keys**: `service:entity:id:attribute` prevents collisions.
 
**Use appropriate data structures**: Don't store JSON in a Hash if you always read all fields; use a string. Don't use a list when you need O(1) membership checks; use a set.
 
**Pipeline for batch operations**: Always pipeline independent operations.
 
**Use UNLINK instead of DEL for large keys**: UNLINK is non-blocking.
 
**Lazy deletion for large collections**: `UNLINK` + `OBJECT ENCODING` before deleting large keys.
 
**Separate Redis instances by concern**: One for caching (volatile, LRU eviction), one for session storage (no eviction), one for queuing (persistence enabled).
 
### Anti-Patterns
 
**Storing too much in one key**: A hash with 100,000 fields is hard to manage and can block Redis on `HGETALL`.
 
**Using Redis as a primary database**: Redis is not durable enough for data you cannot regenerate. Use it as a cache or for ephemeral data.
 
**Ignoring memory limits**: Not setting `maxmemory` means Redis will use all available RAM and the OS will start killing processes.
 
**Using `SELECT` for multi-tenancy**: Redis DB selection (`SELECT 0-15`) is not a proper isolation mechanism. Use separate Redis instances or key prefixes.
 
**Polling instead of pub/sub or streams**: Polling with `GET` in a loop wastes CPU and adds latency. Use `BLPOP`, pub/sub, or streams.
 
**Storing sessions without TTL**: Sessions with no expiry accumulate and fill memory.
 
**No error handling for cache misses**: Always handle the case where a key does not exist.
  
## 24. Celery Introduction and Architecture
 
### What is Celery?
 
Celery is an open-source, distributed task queue for Python. It enables applications to execute tasks asynchronously in the background, outside of the request-response cycle, and schedule tasks to run at specific times.
 
**Core use cases:**
 
 
- **Async tasks**: Send email, process image, generate report — without blocking the HTTP response.
 
- **Background jobs**: Data import, cleanup, indexing.
 
- **Periodic tasks**: Cron-like scheduled jobs (generate daily reports, send newsletters).
 
- **Distributed processing**: Distribute heavy computation across multiple workers.
 
- **Rate-limited processing**: Process tasks at a controlled rate (e.g., API rate limits).
 
- **Chaining/orchestrating workflows**: Execute tasks in sequence or parallel with dependencies.
 

 
### Celery Architecture
 `                    ┌─────────────────────┐                     │   Application       │                     │  (Django/FastAPI)   │                     └─────────┬───────────┘                               │ .delay() / .apply_async()                               ▼                     ┌─────────────────────┐                     │   Message Broker    │  ← Redis, RabbitMQ, SQS                     │  (Queue/Topic)      │                     └─────────┬───────────┘                               │                ┌──────────────┼──────────────┐                ▼              ▼              ▼         ┌─────────┐    ┌─────────┐    ┌─────────┐         │ Worker 1│    │ Worker 2│    │ Worker 3│         └────┬────┘    └────┬────┘    └────┬────┘              │              │              │              └──────────────┴──────────────┘                               │                               ▼                     ┌─────────────────────┐                     │   Result Backend    │  ← Redis, DB, Memcached                     │  (Task results)     │                     └─────────────────────┘ ` 
**Components:**
 
 
- **Producer**: Application code that calls `.delay()` or `.apply_async()` to enqueue a task.
 
- **Broker**: Message queue that holds tasks awaiting execution. Redis or RabbitMQ.
 
- **Worker**: Process that consumes tasks from the broker and executes them.
 
- **Result Backend**: Stores task results (return values, errors, state). Optional.
 
- **Beat**: Celery's scheduler. Sends periodic tasks to the broker on a schedule.
 
- **Flower**: Web-based monitoring and management tool.
 

  
## 25. Celery Tasks
 
### Defining Tasks
 `# tasks.py from celery import Celery  app = Celery('myapp', broker='redis://localhost:6379/0')  @app.task def add(x, y):     return x + y  @app.task def send_email(user_id: int, subject: str, body: str):     user = User.objects.get(id=user_id)     email.send(to=user.email, subject=subject, body=body)  # With options @app.task(     name='tasks.process_image',     # Explicit name (stable across refactors)     bind=True,                       # Access self (task instance)     max_retries=3,     default_retry_delay=60,         # seconds     time_limit=300,                 # hard time limit (seconds)     soft_time_limit=240,            # soft limit (raises SoftTimeLimitExceeded)     rate_limit='10/m',              # max 10 executions per minute     queue='images',                 # specific queue     ignore_result=True,             # don't store result     acks_late=True,                 # ack after completion (safer)     reject_on_worker_lost=True,     # re-queue if worker dies ) def process_image(self, image_id: int):     try:         image = Image.objects.get(id=image_id)         image.process()     except SoftTimeLimitExceeded:         # Cleanup before hard kill         image.mark_failed()     except Exception as exc:         raise self.retry(exc=exc) ` 
### Calling Tasks
 `# Fire and forget add.delay(4, 6)  # With options add.apply_async(     args=[4, 6],     kwargs={},     countdown=60,           # Delay 60 seconds     eta=datetime(2024, 1, 1, 12, 0),  # Execute at specific time     expires=3600,           # Task expires if not started within 1 hour     queue='high-priority',     priority=9,             # 0-9, higher = higher priority (RabbitMQ)     serializer='json',     compression='gzip',     retry=True,     retry_policy={         'max_retries': 3,         'interval_start': 0,         'interval_step': 0.2,         'interval_max': 0.5,     }, )  # Get result (blocking) result = add.delay(4, 6) value = result.get(timeout=10)   # Wait up to 10 seconds  # Check result state result.state     # PENDING | STARTED | SUCCESS | FAILURE | RETRY | REVOKED result.ready()   # True if finished result.successful() result.failed() result.result    # Return value or exception  # Revoke (cancel) a task result.revoke(terminate=True, signal='SIGTERM') ` 
### Task States
 `PENDING  → Task not yet received by a worker (or unknown ID) RECEIVED → Task received by worker STARTED  → Worker has begun executing SUCCESS  → Task completed successfully; result stored FAILURE  → Task raised an exception RETRY    → Task is being retried REVOKED  → Task was cancelled ` 
### Task Signatures (Partial Application)
 `# Create a signature (not yet executed) sig = add.s(4, 6)       # Positional args sig = add.s(4, y=6)     # Mixed sig = add.si(4, 6)      # Immutable signature (ignores piped results)  # Execute the signature sig.delay() sig.apply_async() `  
## 26. Celery Brokers and Backends
 
### Redis as Broker
 `app = Celery('myapp',     broker='redis://:password@localhost:6379/0',     backend='redis://:password@localhost:6379/1' )  # With Sentinel app = Celery('myapp',     broker='sentinel://:password@sentinel-1:26379;sentinel-2:26379/mymaster/0', )  # With TLS app = Celery('myapp',     broker='rediss://localhost:6380/0'  # rediss:// = Redis + TLS ) ` 
### RabbitMQ as Broker
 `app = Celery('myapp',     broker='amqp://user:password@localhost:5672/vhost',     backend='redis://localhost:6379/1' ) ` 
RabbitMQ supports more advanced routing (exchanges, binding keys) and task priorities natively. Redis is simpler and works well for most use cases.
 
### Redis as Result Backend
 `# Configuration app.conf.result_backend = 'redis://localhost:6379/1' app.conf.result_expires = 3600    # Results expire after 1 hour app.conf.result_persistent = False  # Don't persist results across restarts ` 
**Result backend stores:**
 
 
- Task ID → state mapping
 
- Task ID → return value (serialized)
 
- Task ID → exception info (if failed)
 

 
### Database as Result Backend (Django)
 `app.conf.result_backend = 'django-db'  # Requires django-celery-results ` 
### Disabling Result Backend
 
If you don't need task results:
 `@app.task(ignore_result=True) def fire_and_forget(data):     ... `  
## 27. Celery Configuration
 
### Full Configuration Reference
 `# celery.py or settings.py (Django)  app.conf.update(     # Broker     broker_url='redis://localhost:6379/0',     broker_connection_retry_on_startup=True,     broker_connection_max_retries=10,     broker_pool_limit=10,           # Connection pool size     broker_heartbeat=10,            # Heartbeat interval (RabbitMQ)     broker_transport_options={         'visibility_timeout': 3600,   # Redis: how long a task can run before re-queued         'max_retries': 3,     },          # Result Backend     result_backend='redis://localhost:6379/1',     result_expires=3600,     result_serializer='json',     result_compression=None,          # Tasks     task_serializer='json',     task_compression=None,     task_always_eager=False,          # Set True in tests (run synchronously)     task_eager_propagates=True,       # Propagate exceptions in eager mode     task_acks_late=False,             # Ack after execution (True = at-least-once)     task_reject_on_worker_lost=False, # Re-queue if worker dies (requires acks_late)     task_track_started=True,          # Update state to STARTED when worker picks up     task_soft_time_limit=None,     task_time_limit=None,     task_max_retries=3,     task_default_queue='default',     task_queues=(         Queue('default'),         Queue('high-priority', routing_key='high'),         Queue('low-priority', routing_key='low'),     ),     task_routes={         'tasks.send_email': {'queue': 'emails'},         'tasks.process_image': {'queue': 'images'},     },          # Worker     worker_concurrency=4,             # Processes/threads (default: CPU count)     worker_prefetch_multiplier=4,     # How many tasks to prefetch per worker     worker_max_tasks_per_child=1000,  # Restart worker after N tasks (memory leaks)     worker_max_memory_per_child=200000,  # Restart if uses > 200 MB     worker_disable_rate_limits=False,     worker_send_task_events=True,     # Needed for Flower monitoring     task_send_sent_event=True,        # Track sent events          # Serialization     accept_content=['json'],           # Only accept JSON (security)          # Time zones     timezone='UTC',     enable_utc=True,          # Beat (scheduler)     beat_schedule={         'daily-report': {             'task': 'tasks.generate_report',             'schedule': crontab(hour=8, minute=0),         },     },     beat_schedule_filename='celerybeat-schedule', ) ` 
### Worker Execution Pool Types
 
  
 
Pool
 
Description
 
Use Case
 
   
 
`prefork`
 
Multi-process (default). Uses `fork()`.
 
CPU-bound tasks
 
 
 
`gevent`
 
Green threads via gevent.
 
I/O-bound tasks, high concurrency
 
 
 
`eventlet`
 
Green threads via eventlet.
 
I/O-bound tasks
 
 
 
`solo`
 
Single-threaded.
 
Debugging, embedded
 
 
 
`threads`
 
Thread pool.
 
I/O-bound with GIL awareness
 
  
 `celery -A myapp worker --pool=gevent --concurrency=100 `  
## 28. Celery Routing and Queues
 
### Queue Definitions
 `from kombu import Queue, Exchange  app.conf.task_queues = [     Queue('default',        routing_key='default'),     Queue('high-priority',  routing_key='high'),     Queue('emails',         routing_key='emails'),     Queue('images',         routing_key='images'),     Queue('cpu-intensive',  routing_key='cpu'), ]  app.conf.task_default_queue = 'default' ` 
### Routing Tasks to Queues
 
**Static routing (in task definition):**
 `@app.task(queue='high-priority') def urgent_task():     ... ` 
**Route map (in config):**
 `app.conf.task_routes = {     'tasks.send_email': {'queue': 'emails'},     'tasks.send_*': {'queue': 'emails'},       # Glob pattern     'tasks.process_*': {'queue': 'cpu-intensive'}, } ` 
**Dynamic routing (at call time):**
 `send_email.apply_async(args=[user_id], queue='high-priority') ` 
### Running Workers on Specific Queues
 `# Process only 'emails' and 'default' queues celery -A myapp worker -Q emails,default  # One worker per queue type celery -A myapp worker -Q high-priority --concurrency=2 -n worker-high@%h celery -A myapp worker -Q emails --concurrency=4 -n worker-email@%h celery -A myapp worker -Q default --concurrency=8 -n worker-default@%h `  
## 29. Celery Scheduling: Beat
 
Celery Beat is a scheduler that sends tasks to the broker on a defined schedule.
 
### Schedule Types
 `from celery.schedules import crontab, solar from datetime import timedelta  app.conf.beat_schedule = {     # Every 10 seconds     'every-10-seconds': {         'task': 'tasks.heartbeat',         'schedule': 10.0,     },          # Every 5 minutes     'every-5-minutes': {         'task': 'tasks.sync_data',         'schedule': timedelta(minutes=5),         'args': ('source_a',),     },          # Every day at 8:00 AM UTC     'daily-report': {         'task': 'tasks.daily_report',         'schedule': crontab(hour=8, minute=0),     },          # Every Monday at midnight     'weekly-cleanup': {         'task': 'tasks.cleanup',         'schedule': crontab(hour=0, minute=0, day_of_week='monday'),     },          # First day of every month at 00:00     'monthly-billing': {         'task': 'tasks.process_billing',         'schedule': crontab(hour=0, minute=0, day_of_month=1),     },          # Crontab syntax: minute hour day_of_week day_of_month month_of_year     # All support '*', '*/n', 'a,b,c', 'a-b' syntax          # Solar schedules (sunrise/sunset)     'at-sunrise': {         'task': 'tasks.morning_routine',         'schedule': solar('sunrise', -37.81, 144.96),  # Melbourne     }, } ` 
### Starting Beat
 `celery -A myapp beat -l INFO  # With database scheduler (allows runtime schedule modification) pip install django-celery-beat celery -A myapp beat -l INFO --scheduler django_celery_beat.schedulers:DatabaseScheduler ` 
**Important**: Only run ONE beat instance. Multiple beat instances will send duplicate tasks.
 
### Dynamic Schedules with django-celery-beat
 `from django_celery_beat.models import PeriodicTask, IntervalSchedule, CrontabSchedule import json  # Create interval schedule schedule, _ = IntervalSchedule.objects.get_or_create(     every=10,     period=IntervalSchedule.SECONDS, )  # Create periodic task PeriodicTask.objects.create(     interval=schedule,     name='my-dynamic-task',     task='tasks.my_task',     args=json.dumps([42]),     kwargs=json.dumps({'user': 'alice'}),     expires=timezone.now() + timedelta(hours=1), ) `  
## 30. Celery Monitoring and Flower
 
### Flower (Web UI)
 `pip install flower  # Start Flower celery -A myapp flower --port=5555  # With authentication celery -A myapp flower --basic_auth=user:password  # Persistent database celery -A myapp flower --persistent --db=flower_db ` 
Flower provides:
 
 
- Real-time task tracking (state, args, result, runtime)
 
- Worker status and concurrency
 
- Queue lengths
 
- Task throughput charts
 
- Ability to revoke tasks
 
- Worker shutdown/restart
 

 
### Celery CLI Inspection
 `# Worker status celery -A myapp status celery -A myapp inspect active           # Currently executing tasks celery -A myapp inspect reserved         # Tasks prefetched but not started celery -A myapp inspect scheduled        # Countdown/ETA tasks celery -A myapp inspect registered       # Registered task names celery -A myapp inspect stats            # Worker statistics celery -A myapp inspect query_task <id>  # Specific task status  # Control workers celery -A myapp control rate_limit tasks.process_image 10/m  # Set rate limit celery -A myapp control shutdown         # Graceful shutdown all workers celery -A myapp control pool_grow 4      # Add 4 processes to pool celery -A myapp control pool_shrink 2    # Remove 2 processes  # Purge queues celery -A myapp purge                    # Purge all queues  # Events celery -A myapp events                   # Real-time event stream ` 
### Programmatic Inspection
 `from celery.app.control import Control, Inspect  i = app.control.inspect()  # Get active tasks active = i.active() print(active)  # Get queue stats reserved = i.reserved()  # Revoke a task app.control.revoke(task_id, terminate=True)  # Broadcast shutdown app.control.broadcast('shutdown') `  
## 31. Celery Error Handling and Retries
 
### Automatic Retries
 `@app.task(     autoretry_for=(Exception,),           # Auto-retry on any exception     max_retries=3,     retry_backoff=True,                   # Exponential backoff     retry_backoff_max=600,                # Max delay 10 minutes     retry_jitter=True,                    # Add randomness to backoff ) def fetch_from_api(url: str) -> dict:     response = requests.get(url, timeout=10)     response.raise_for_status()     return response.json() ` 
### Manual Retries
 `@app.task(bind=True, max_retries=5) def process_payment(self, payment_id: int) -> None:     try:         payment = Payment.objects.get(id=payment_id)         payment_gateway.charge(payment)     except PaymentGatewayTemporaryError as exc:         # Retry with exponential backoff         raise self.retry(             exc=exc,             countdown=2 ** self.request.retries,  # 1, 2, 4, 8, 16 seconds         )     except PaymentGatewayPermanentError as exc:         # Don't retry — log and mark as failed         payment.mark_failed(str(exc))         return     except Exception as exc:         raise self.retry(exc=exc, countdown=60) ` 
### Error Callbacks
 `@app.task def on_failure_handler(self, exc, task_id, args, kwargs, einfo):     print(f"Task {task_id} failed: {exc}")     alert_team(task_id, exc, einfo)  @app.task(on_failure=on_failure_handler) def critical_task():     ...  # Or as signals from celery.signals import task_failure, task_success, task_retry  @task_failure.connect def handle_task_failure(sender=None, task_id=None, exception=None, **kwargs):     logger.error(f"Task {task_id} failed: {exception}")  @task_success.connect def handle_task_success(sender=None, result=None, **kwargs):     metrics.increment('celery.task.success')  @task_retry.connect def handle_task_retry(sender=None, reason=None, **kwargs):     metrics.increment('celery.task.retry') ` 
### Dead Letter Handling
 `from celery.exceptions import MaxRetriesExceededError  @app.task(bind=True, max_retries=3) def process_item(self, item_id: int) -> None:     try:         process(item_id)     except Exception as exc:         try:             raise self.retry(exc=exc, countdown=60)         except MaxRetriesExceededError:             # Move to dead letter processing             DeadLetterQueue.objects.create(                 item_id=item_id,                 error=str(exc),                 task_id=self.request.id,             ) ` 
### Task Timeouts
 `@app.task(     soft_time_limit=55,   # Raises SoftTimeLimitExceeded; allows cleanup     time_limit=60,        # Hard kill with SIGKILL after 60s ) def long_running_task():     try:         do_work()     except SoftTimeLimitExceeded:         cleanup()         raise `  
## 32. Celery Workflows: Chains, Groups, Chords
 
### Chain (Sequential Pipeline)
 
Tasks execute one after another; each task receives the result of the previous.
 `from celery import chain, group, chord, signature  # Execute in sequence: fetch → parse → save workflow = chain(     fetch_data.s(url),     parse_data.s(),       # receives result of fetch_data     save_to_db.s(),       # receives result of parse_data ) result = workflow.delay() final = result.get() ` 
### Group (Parallel)
 
Multiple tasks execute simultaneously and return a list of results.
 `# Process 3 URLs in parallel job = group(     fetch_data.s(url1),     fetch_data.s(url2),     fetch_data.s(url3), ) result = job.apply_async() results = result.get()  # [result1, result2, result3] ` 
### Chord (Parallel + Callback)
 
A group with a callback that executes after all tasks in the group complete.
 `# Process N items in parallel, then aggregate results job = chord(     group(process_item.s(item) for item in items),     aggregate_results.s()   # Called with list of all results ) final_result = job.delay() ` 
### Complex Workflows
 `# Parallel fetch, then process in sequence, then save pipeline = chain(     group(         fetch_data.s('source_1'),         fetch_data.s('source_2'),         fetch_data.s('source_3'),     ),     merge_data.s(),          # merges list of results     clean_data.s(),     save_to_db.s(), )  # Chord inside chain pipeline = chain(     prepare_data.s(dataset_id),     chord(         group(process_chunk.s(i) for i in range(10)),         combine_chunks.s()     ),     finalize.s() ) ` 
### Immutable Signatures
 
When chaining, by default each task receives the result of the previous. Use `.si()` to ignore piped input:
 `chain(     compute_result.s(data),     log_to_analytics.si("task-completed"),  # runs after but ignores result     send_notification.si(user_id),          # also independent ) `  
## 33. Celery with Django and FastAPI
 
### Django Integration
 `# myproject/celery.py import os from celery import Celery  os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')  app = Celery('myproject') app.config_from_object('django.conf:settings', namespace='CELERY') app.autodiscover_tasks()  # Auto-find tasks in each app's tasks.py  # myproject/__init__.py from .celery import app as celery_app __all__ = ('celery_app',)  # settings.py CELERY_BROKER_URL = 'redis://localhost:6379/0' CELERY_RESULT_BACKEND = 'redis://localhost:6379/1' CELERY_ACCEPT_CONTENT = ['json'] CELERY_TASK_SERIALIZER = 'json' CELERY_RESULT_SERIALIZER = 'json' CELERY_TIMEZONE = 'UTC'  # myapp/tasks.py from myproject.celery import app  @app.task def send_welcome_email(user_id: int):     from myapp.models import User  # Import inside task to avoid circular imports     user = User.objects.get(id=user_id)     # ... send email ` 
### FastAPI Integration
 `# celery_app.py from celery import Celery  celery_app = Celery(     'worker',     broker='redis://localhost:6379/0',     backend='redis://localhost:6379/1',     include=['app.tasks'] )  # tasks.py from celery_app import celery_app  @celery_app.task def process_data(data: dict) -> dict:     # Heavy processing     return result  # main.py (FastAPI) from fastapi import FastAPI, BackgroundTasks from tasks import process_data  app = FastAPI()  @app.post("/process") async def process_endpoint(data: dict):     # Enqueue Celery task     task = process_data.delay(data)     return {"task_id": task.id, "status": "queued"}  @app.get("/result/{task_id}") async def get_result(task_id: str):     from celery.result import AsyncResult     result = AsyncResult(task_id, app=celery_app)     if result.ready():         return {"status": result.state, "result": result.result}     return {"status": result.state} `  
## 34. Celery Production Patterns
 
### Supervisor Configuration
 `; /etc/supervisor/conf.d/celery.conf [program:celery-worker] command=/venv/bin/celery -A myapp worker -l INFO -Q default --concurrency=4 directory=/app user=celery numprocs=1 autostart=true autorestart=true startsecs=10 stopwaitsecs=600 killasgroup=true priority=998 stdout_logfile=/var/log/celery/worker.log stderr_logfile=/var/log/celery/worker_error.log  [program:celery-beat] command=/venv/bin/celery -A myapp beat -l INFO directory=/app user=celery autostart=true autorestart=true stdout_logfile=/var/log/celery/beat.log ` 
### Docker / Docker Compose
 `# docker-compose.yml services:   redis:     image: redis:7-alpine     ports:       - "6379:6379"     volumes:       - redis_data:/data     command: redis-server --appendonly yes    app:     build: .     depends_on:       - redis    celery-worker:     build: .     command: celery -A myapp worker -l INFO --concurrency=4     depends_on:       - redis       - app     environment:       - CELERY_BROKER_URL=redis://redis:6379/0       - CELERY_RESULT_BACKEND=redis://redis:6379/1     volumes:       - .:/app    celery-beat:     build: .     command: celery -A myapp beat -l INFO     depends_on:       - redis       - app    flower:     build: .     command: celery -A myapp flower --port=5555     ports:       - "5555:5555"     depends_on:       - redis  volumes:   redis_data: ` 
### Kubernetes Deployment
 `# celery-worker-deployment.yaml apiVersion: apps/v1 kind: Deployment metadata:   name: celery-worker spec:   replicas: 3   selector:     matchLabels:       app: celery-worker   template:     metadata:       labels:         app: celery-worker     spec:       containers:       - name: celery-worker         image: myapp:latest         command: ["celery", "-A", "myapp", "worker", "-l", "INFO", "-Q", "default"]         resources:           requests:             memory: "256Mi"             cpu: "500m"           limits:             memory: "512Mi"             cpu: "1000m"         env:         - name: CELERY_BROKER_URL           valueFrom:             secretKeyRef:               name: app-secrets               key: redis-url         livenessProbe:           exec:             command: ["celery", "-A", "myapp", "inspect", "ping"]           initialDelaySeconds: 30           periodSeconds: 60 ` 
### Graceful Shutdown
 `# Send SIGTERM for graceful shutdown (finishes current tasks) kill -SIGTERM $(pgrep -f 'celery worker')  # With supervisor supervisorctl stop celery-worker  # Celery will: # 1. Stop accepting new tasks # 2. Wait for current tasks to finish (up to stopwaitsecs) # 3. Exit `  
## 35. Redis + Celery Integration Patterns
 
### Cache-Through with Celery Refresh
 
Async cache population via Celery for expensive computations:
 `# tasks.py @app.task def refresh_user_recommendations(user_id: int) -> None:     recommendations = ml_model.get_recommendations(user_id)     r.setex(f"recommendations:{user_id}", 3600, json.dumps(recommendations))  # views.py def get_recommendations(user_id: int) -> list:     cached = r.get(f"recommendations:{user_id}")     if cached:         return json.loads(cached)          # Return empty/default while async generation happens     refresh_user_recommendations.delay(user_id)     return get_default_recommendations() ` 
### Celery Task Deduplication with Redis
 
Prevent the same task from being queued multiple times:
 `@app.task(bind=True) def process_user_export(self, user_id: int) -> None:     lock_key = f"lock:task:export:{user_id}"          # Check if task already running     if r.exists(lock_key):         return  # Duplicate, skip          # Set lock for duration of expected task execution     r.setex(lock_key, 3600, self.request.id)     try:         generate_export(user_id)     finally:         r.delete(lock_key)  # Or as a decorator from functools import wraps  def unique_task(timeout=3600):     def decorator(task_func):         @wraps(task_func)         def wrapper(self, *args, **kwargs):             lock_key = f"unique_task:{self.name}:{args}:{kwargs}"             if not r.set(lock_key, "1", nx=True, ex=timeout):                 return  # Task already queued/running             try:                 return task_func(self, *args, **kwargs)             finally:                 r.delete(lock_key)         return wrapper     return decorator ` 
### Celery Task Progress with Redis
 
Track granular task progress:
 `@app.task(bind=True) def process_large_file(self, file_id: int) -> dict:     progress_key = f"task:progress:{self.request.id}"     items = load_file(file_id)     total = len(items)          for i, item in enumerate(items):         process_item(item)                  # Update progress in Redis         r.hset(progress_key, mapping={             'current': i + 1,             'total': total,             'percent': round((i + 1) / total * 100, 1),             'status': 'processing',         })         r.expire(progress_key, 3600)          r.hset(progress_key, mapping={'status': 'done', 'percent': 100})     return {'processed': total}  # API endpoint to poll progress def get_task_progress(task_id: str) -> dict:     progress_key = f"task:progress:{task_id}"     progress = r.hgetall(progress_key)     if not progress:         result = AsyncResult(task_id)         return {'status': result.state}     return progress ` 
### Distributed Rate Limiting with Celery
 
Control how fast Celery processes tasks from external APIs:
 `@app.task(rate_limit='10/s')  # Celery-level rate limit per worker def call_external_api(endpoint: str) -> dict:     return requests.get(endpoint).json()  # Global rate limit across all workers using Redis @app.task(bind=True) def rate_limited_api_call(self, endpoint: str) -> dict:     bucket_key = "ratelimit:external_api"          # Token bucket: 100 requests per minute     tokens = r.get(bucket_key)     if tokens is None or int(tokens) <= 0:         # Backoff and retry         raise self.retry(countdown=60)          r.decr(bucket_key)     return requests.get(endpoint).json()  # Refill tokens every minute @app.on_after_configure.connect def setup_refill(sender, **kwargs):     sender.add_periodic_task(60.0, refill_api_tokens.s(), name='refill-api-tokens')  @app.task def refill_api_tokens():     r.set("ratelimit:external_api", 100, ex=120) `  
## 36. Observability and Debugging
 
### Redis Monitoring
 
**Key metrics to monitor:**
 
  
 
Metric
 
Command
 
Alert threshold
 
   
 
Memory usage
 
`INFO memory` → `used_memory`
 
> 80% of maxmemory
 
 
 
Hit rate
 
`INFO stats` → `keyspace_hits / (keyspace_hits + keyspace_misses)`
 
< 90%
 
 
 
Evictions
 
`INFO stats` → `evicted_keys`
 
Any eviction (may indicate memory pressure)
 
 
 
Connected clients
 
`INFO clients` → `connected_clients`
 
> 80% of maxclients
 
 
 
Blocked clients
 
`INFO clients` → `blocked_clients`
 
Sustained high count
 
 
 
Replication lag
 
`INFO replication` → `master_repl_offset - slave_repl_offset`
 
> 1 MB
 
 
 
Command latency
 
`LATENCY LATEST`
 
P99 > 1 ms
 
 
 
Slow commands
 
`SLOWLOG LEN`
 
Growing count
 
 
 
RDB save time
 
`INFO persistence` → `rdb_last_bgsave_time_sec`
 
> 30 seconds
 
  
 
**Prometheus/Grafana integration:**
 `# redis_exporter docker run -d -p 9121:9121 \   -e REDIS_ADDR=redis://localhost:6379 \   oliver006/redis_exporter ` 
### Celery Monitoring
 
**Key metrics:**
 
  
 
Metric
 
Source
 
   
 
Queue length
 
`celery inspect reserved` or Redis `LLEN queue_name`
 
 
 
Active tasks
 
`celery inspect active`
 
 
 
Worker count
 
`celery inspect ping`
 
 
 
Task success rate
 
Celery signals / task_success count
 
 
 
Task failure rate
 
Celery signals / task_failure count
 
 
 
Task processing time
 
task_success event timing
 
 
 
Retry count
 
task_retry events
 
  
 
**Prometheus integration (celery-prometheus-exporter):**
 `from celery.signals import task_postrun, task_failure from prometheus_client import Counter, Histogram  tasks_processed = Counter('celery_tasks_total', 'Total tasks', ['task', 'state']) task_duration = Histogram('celery_task_duration_seconds', 'Task duration', ['task'])  @task_postrun.connect def track_task(sender=None, state=None, **kwargs):     tasks_processed.labels(task=sender, state=state).inc() ` 
### Debugging Celery Tasks
 `# Run tasks synchronously (no broker needed, for testing) app.conf.task_always_eager = True add.delay(2, 3)  # Runs immediately, returns result directly  # Enable task events for real-time debugging app.conf.worker_send_task_events = True app.conf.task_send_sent_event = True  # Logging import logging logger = logging.getLogger(__name__)  @app.task def my_task(data):     logger.info(f"Processing task with data: {data}")     result = process(data)     logger.info(f"Task completed with result: {result}")     return result  # Debug a specific task (run in shell) from myapp.tasks import my_task my_task.apply(args=[data])  # Synchronous, in-process `  
## 37. Advanced Topics
 
### Redis Modules
 
Redis Stack extends Redis with additional capabilities:
 
 
- **RediSearch**: Full-text search, vector similarity search (for AI/ML), secondary indexing.
 
- **RedisJSON**: Native JSON storage and querying. Partial updates, JSONPath support.
 
- **RedisTimeSeries**: Time-series data with downsampling, aggregation.
 
- **RedisBloom**: Bloom filter, Cuckoo filter, Count-Min sketch, Top-K.
 
- **RedisGraph**: Property graph database (based on sparse matrices).
 

 `# RedisJSON example JSON.SET user:42 $ '{"name":"Alice","age":30,"address":{"city":"NYC"}}' JSON.GET user:42 $.name JSON.SET user:42 $.age 31           # Partial update JSON.ARRAPPEND user:42 $.hobbies "chess"  # RediSearch example FT.CREATE idx ON JSON PREFIX 1 user: SCHEMA $.name AS name TEXT $.age AS age NUMERIC FT.SEARCH idx "Alice" RETURN 2 name age FT.SEARCH idx "@age:[25 35]" SORTBY age ASC ` 
### Keyspace Notifications
 
Subscribe to notifications about Redis key events:
 `# redis.conf notify-keyspace-events "KEA"   # K=keyspace, E=keyevent, A=all events # Options: K(keyspace), E(keyevent), g(generic), $(string), l(list), s(set), h(hash), z(zset), x(expired), d(deleted) ` `pubsub = r.pubsub() # Subscribe to all expired key events pubsub.psubscribe('__keyevent@0__:expired') for message in pubsub.listen():     if message['type'] == 'pmessage':         expired_key = message['data']         print(f"Key expired: {expired_key}") ` 
**Use cases:** Session expiry notifications, TTL-based event triggers, cache miss tracking.
 
### RESP3 Protocol and Client-Side Caching
 
Redis 6+ supports RESP3 (Redis Serialization Protocol v3), which enables client-side caching via server-assisted invalidation:
 
 
- Client tells Redis: "I'm caching these keys. Notify me if they change."
 
- Redis sends invalidation messages when cached keys are modified.
 
- Client invalidates local cache entry.
 

 `CLIENT TRACKING on REDIRECT <client-id>  # Enable tracking CLIENT CACHING yes                        # Opt-in to cache current request ` 
This eliminates the need for explicit TTL-based invalidation in many cases.
 
### Celery Canvas and Complex DAGs
 `from celery import group, chain, chord, signature  # DAG: fetch data, transform in parallel, then merge dag = chain(     fetch_raw_data.s(source_id),     group(         transform_format_a.s(),         transform_format_b.s(),         transform_format_c.s(),     ),     chord(         group(validate_record.s(record) for record in []),  # Dynamic         save_validated_records.s()     ),     notify_completion.si(user_id=42) ) ` 
### Celery + Redis Streams as Broker
 
Using Redis Streams as a more durable alternative to Redis list-based queuing:
 `# In newer Celery versions, you can configure the kombu transport to use streams # This provides better delivery guarantees and replay capability app.conf.broker_transport_options = {     'stream_name': 'celery-tasks',     'consumer_group': 'celery-workers', } ` 
### Multi-Level Caching Architecture
 
Production systems often use multiple cache layers:
 `import functools  class TieredCache:     def __init__(self, local_ttl=5, redis_ttl=300):         self.local_cache: dict = {}         self.local_ttl = local_ttl         self.redis_ttl = redis_ttl          def get(self, key: str) -> any:         # L1: in-process dict         entry = self.local_cache.get(key)         if entry and entry['exp'] > time.time():             return entry['value']                  # L2: Redis         redis_val = r.get(key)         if redis_val:             val = json.loads(redis_val)             self._set_local(key, val)             return val                  return None          def set(self, key: str, value: any, ttl: int | None = None) -> None:         self._set_local(key, value)         r.setex(key, ttl or self.redis_ttl, json.dumps(value))          def _set_local(self, key: str, value: any) -> None:         self.local_cache[key] = {             'value': value,             'exp': time.time() + self.local_ttl         }          def delete(self, key: str) -> None:         self.local_cache.pop(key, None)         r.delete(key)  cache = TieredCache(local_ttl=5, redis_ttl=300) ` 
### Consistent Hashing for Cache Sharding
 
When using multiple Redis instances without Redis Cluster:
 `import hashlib import bisect  class ConsistentHashRing:     def __init__(self, nodes: list[str], virtual_nodes: int = 150):         self.virtual_nodes = virtual_nodes         self.ring: dict[int, str] = {}         self.sorted_keys: list[int] = []                  for node in nodes:             self.add_node(node)          def _hash(self, key: str) -> int:         return int(hashlib.md5(key.encode()).hexdigest(), 16)          def add_node(self, node: str) -> None:         for i in range(self.virtual_nodes):             virtual_key = self._hash(f"{node}:{i}")             self.ring[virtual_key] = node             bisect.insort(self.sorted_keys, virtual_key)          def remove_node(self, node: str) -> None:         for i in range(self.virtual_nodes):             virtual_key = self._hash(f"{node}:{i}")             del self.ring[virtual_key]             self.sorted_keys.remove(virtual_key)          def get_node(self, key: str) -> str:         if not self.ring:             raise ValueError("Ring is empty")         hash_key = self._hash(key)         idx = bisect.bisect(self.sorted_keys, hash_key) % len(self.sorted_keys)         return self.ring[self.sorted_keys[idx]]  # Usage ring = ConsistentHashRing(['redis-1:6379', 'redis-2:6379', 'redis-3:6379']) node = ring.get_node("user:42:profile")  # Deterministic node assignment `  
## 38. Quick Reference Cheat Sheet
 
### Redis CLI Quick Reference
 `redis-cli                          # Connect to localhost:6379 redis-cli -h host -p port -a pass redis-cli -u redis://user:pass@host:port/db redis-cli --tls -h host -p port  redis-cli PING                     # → PONG redis-cli INFO server | grep version redis-cli -r 100 -i 0.1 INFO memory  # Repeat 100x with 0.1s interval redis-cli --bigkeys               # Find large keys redis-cli --hotkeys               # Find frequently accessed keys (LFU only) redis-cli --memkeys               # Memory usage per key redis-cli --scan --pattern "user:*" | head -20 redis-cli --pipe < commands.txt   # Pipe mode for bulk imports redis-cli --csv SMEMBERS myset   # Output as CSV redis-cli DEBUG OBJECT key        # Show object metadata ` 
### Common Redis Patterns Summary
 
  
 
Use Case
 
Data Structure
 
Key Pattern
 
   
 
Simple cache
 
String
 
`entity:id:attr`
 
 
 
Object cache
 
Hash
 
`entity:id`
 
 
 
Ordered feed
 
List
 
`user:id:feed`
 
 
 
Unique tags
 
Set
 
`entity:id:tags`
 
 
 
Leaderboard
 
Sorted Set
 
`leaderboard:name`
 
 
 
Rate limiter
 
String (INCR)
 
`ratelimit:user:id:window`
 
 
 
Session
 
Hash
 
`session:token`
 
 
 
Pub/Sub
 
Pub/Sub
 
`channel:name`
 
 
 
Job queue
 
List (BRPOP)
 
`queue:name`
 
 
 
Priority queue
 
Sorted Set
 
`priority:queue:name`
 
 
 
Unique visitors
 
HyperLogLog
 
`visits:date`
 
 
 
Geosearch
 
Geo
 
`locations:name`
 
 
 
Event log
 
Stream
 
`events:stream`
 
 
 
Distributed lock
 
String (NX)
 
`lock:resource`
 
 
 
Bloom filter
 
RedisBloom BF
 
`exists:collection`
 
 
 
Counter
 
String (INCR)
 
`count:entity:id`
 
 
 
Expiring set
 
Sorted Set
 
`expiring:set` (score=TTL)
 
  
 
### Celery Task States
 `PENDING  → Task not started or unknown RECEIVED → Worker received task STARTED  → Worker executing (requires task_track_started=True) SUCCESS  → Completed successfully FAILURE  → Raised an exception RETRY    → Being retried REVOKED  → Cancelled ` 
### Celery CLI Quick Reference
 `# Start worker celery -A app worker -l INFO -Q default -c 4 -n worker@%h  # Start beat celery -A app beat -l INFO --scheduler django_celery_beat.schedulers:DatabaseScheduler  # Start flower celery -A app flower --port=5555  # Inspect celery -A app inspect active celery -A app inspect ping celery -A app inspect stats celery -A app status  # Control celery -A app control rate_limit tasks.name 10/m celery -A app control shutdown celery -A app purge -Q queue_name  # Events celery -A app events ` 
### Redis Memory Footprint (Approximate)
 
  
 
Data Type
 
Small Value
 
Large Value
 
   
 
String
 
~60 bytes overhead
 
+ value bytes
 
 
 
Hash
 
~200 bytes (listpack) for ≤128 fields
 
~400+ bytes (hashtable)
 
 
 
List
 
~140 bytes (listpack) for ≤128 elements
 
~200+ bytes (quicklist node)
 
 
 
Set
 
~180 bytes (listpack/intset)
 
~400+ bytes (hashtable)
 
 
 
Sorted Set
 
~200 bytes (listpack) for ≤128 members
 
~400+ bytes (skiplist+hashtable)
 
  
 
### Redis Key Expiration Cheat Sheet
 `r.expire(key, 60)         # 60 seconds r.expire(key, 3600)       # 1 hour r.expire(key, 86400)      # 1 day r.expire(key, 604800)     # 1 week r.expire(key, 2592000)    # 30 days r.persist(key)            # Remove expiry r.ttl(key)                # Get remaining TTL (-1 = no TTL, -2 = not exists) `  
*This document provides a comprehensive reference for caching theory, Redis (from strings to Cluster), and Celery (from basic tasks to complex workflows). It is designed for use as an AI instruction document, interview preparation, and production engineering reference.*