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

![](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/images/ElastiCacheClusters-CSN-RedisShards.png)

## 1.1 Redis Sharding Cluster
- **Write** Scaling using **Sharding**
  - **Number of Shards**: The number of shards in this cluster. A shard is a partition of your data and is comprised of **one primary** and up to five **read replica**，每个切片shard有1个primary节点和最多5个只读节点
- **Read** Scaling using **Read Replicas**
  - **Replicas per Shard**: Input number of replicas per shard between 0 and 5. Zero replicas will not enable an enhanced cluster with primary/replica roles.


## 1.2 Single Redis Cluster
- can have read replicas, BUT no Sharding
- can Multi-AZ


## 1.3 Security:
- Redis support **Redis AUTH (username / password)**
- **SSL in-flight encryption** must be enabled and used
  - Memcached support SASL authentication (advanced)

![](https://i.loli.net/2019/08/21/IiNJsvkqc1nyFw3.png)

-----

![](https://i.loli.net/2019/08/21/DKhp5xcm6najiZF.png)
![](https://i.loli.net/2019/08/21/MeBCazhWyx9tP1r.png)
![](https://i.loli.net/2019/08/21/gYrXkC953jxs6O8.png)

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
store temporary session data in a cache (using TTL features)
用户访问任何一个应用服务器，此应用服务器将Session写入ElastiCache，当用户访问其他应用服务器时，可以在ElastiCache获取之前的session
- User logs into any of the application
- The application writes the session data into ElastiCache
- The user hits another instance of our application
- The instance retrieves the data and the user is already logged in

# 4. Patterns / Cache Strategies for ElastiCache

The combination of lazy loading and write through is ideal.
- If the data expires and is requested, then store the object back in the cache using lazy loading
- if new data was wrote to DB or S3, then trigger a lambda function to cache.


## 4.1 Lazy Loading: Load only when necessary
Lazy loading is a common strategy for caching data, as it prevents you from filling the cache with data that may not be applicable. 

You could further optimize by applying a time-to-live (TTL) value to the data. TTL helps prevent retrieving stale data from the cache. Apply a TTL value that corresponds to the update activity associated to the underlying data.

- Pros
  - Only requested data is cached (the cache isn’t filled up with unused data)只缓存被请求的数据
  - Node failures are not fatal (just increased latency to warm the cache)
- Cons
  - Cache miss penalty that results in 3 round trips, noticeable delay for that request
  - Stale过期 data: data can be updated in the database and outdated in the cache

![](https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2019/03/26/S3_Lazy_Load_2-1024x404.png)

## 4.2 Write Through: Add or Update cache when database is updated
Adds or update data in the cache when written to a DB (no stale data)

The other common technique includes creating a pipeline to update the cache as new data is written to S3, with an AWS Lambda function trigger. This enables you to keep your cache fresh as data is being written to S3. 当写入S3新数据后，出发lambda，去将新数据缓存到redis

- Pros:
  - Data in cache is never stale过期
  - Write penalty vs Read penalty (each write requires 2 calls)
- Cons:
  - Missing Data until it is added / updated in the DB. Mitigation减轻 is to implement Lazy Loading strategy as well
  - Cache churn剧烈搅动 – a lot of the data will never be read

![](https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2019/03/22/write-through2.png)























