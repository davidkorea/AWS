- Helps make your application stateless
- AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups
- None of the caches(redis & memcached) support IAM authentication
- IAM policies on ElastiCache are only used for AWS API-level security
- ElastiCache is helpful for **read-heavy** application workloads, Helps reduce load off of databases for **read intensive workloads**
  - social networks, gaming, media sharing, Q&A portals & compute-intensive workloads (recommendation engines)

# 1. Redis
- Redis is an **in-memory key-value store**
- Super low latency (sub ms)
- **Cache survive** reboots by default (it’s called **persistence**)
- Great to host
  - User sessions
  - Leaderboard (for gaming)
  - Distributed states
  - Relieve pressure on databases (such as RDS)
  - Pub / Sub capability for messaging
- Support for **Read Replicas**
- **Multi AZ** with Failover Capability, ElastiCache Multi-AZ provides enhanced High Availability through automatic **failover to a read replica** in case of a primary node failure，从只读副本进行灾备恢复


## 1.1 Redis Sharding Cluster
- **Write** Scaling using **Sharding**
  - **Number of Shards**: The number of shards in this cluster. A shard is a partition of your data and is comprised of one primary and up to five read replica
- **Read** Scaling using **Read Replicas**
  - **Replicas per Shard**: Input number of replicas per shard between 0 and 5. Zero replicas will not enable an enhanced cluster with primary/replica roles.

## 1.2 Single Redis Cluster
- can have read replicas, BUT no Sharding
- can Multi-AZ


## 1.3 Security:
- Redis support **Redis AUTH (username / password)**
- **SSL in-flight encryption** must be enabled and used
  - Memcached support SASL authentication (advanced)

![](https://i.loli.net/2019/08/21/DKhp5xcm6najiZF.png)

![](https://i.loli.net/2019/08/21/IiNJsvkqc1nyFw3.png)


# 2. Memcached 
- Memcached is an **in-memory object store**
- **Cache doesn’t survive reboots**
- Memcached support SASL authentication (advanced)
- Use cases:
  - Quick retrieval of objects from memory
  - Cache often accessed objects
- Overall, Redis has largely grown in popularity and has better feature sets than Memcached.
- I would personally only use Redis for caching needs. AWS exam wouldn’t ask if Redis or Memcached is better. Just “ElastiCache” in general



# 2. ElastiCache - DB Cache
- Applications queries ElastiCache, if not available, get from RDS and store in ElastiCache.
- Helps relieve减轻 load in RDS
- Cache must have an **invalidation作废 strategy** to make sure only the **most current data** is used in there.


# 3. ElastiCache - User Session Store

用户访问任何一个应用服务器，此应用服务器将Session写入ElastiCache，当用户访问其他应用服务器时，可以在ElastiCache获取之前的session
- User logs into any of the application
- The application writes the session data into ElastiCache
- The user hits another instance of our application
- The instance retrieves the data and the user is already logged in

# 4. Patterns / Cache Strategies for ElastiCache
## 4.1 Lazy Loading: Load only when necessary
all the read data is cached, data can become stale in cache
  
- Pros
  - Only requested data is cached (the cache isn’t filled up with unused data)只缓存被请求的数据
  - Node failures are not fatal (just increased latency to warm the cache)
- Cons
  - Cache miss penalty that results in 3 round trips, noticeable delay for that request
  - Stale过期 data: data can be updated in the database and outdated in the cache


## 4.2 Write Through: Add or Update cache when database is updated
Adds or update data in the cache when written to a DB (no stale data)

- Pros:
  - Data in cache is never stale过期
  - Write penalty vs Read penalty (each write requires 2 calls)
- Cons:
  - Missing Data until it is added / updated in the DB. Mitigation减轻 is to implement Lazy Loading strategy as well
  - Cache churn剧烈搅动 – a lot of the data will never be read


## 4.2 Session Store: store temporary session data in a cache (using TTL features)























