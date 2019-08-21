- Helps make your application stateless
- Helps reduce load off of databases for **read intensive workloads**

- AWS takes care of OS maintenance / patching, optimizations, setup, configuration, monitoring, failure recovery and backups


# 1. Redis

- **Multi AZ** with Failover Capability, ElastiCache Multi-AZ provides enhanced High Availability through automatic **failover to a read replica** in case of a primary node failure，从只读副本进行灾备恢复

## 1.1 Redis Sharding Cluster
- **Write** Scaling using **sharding**
  - **Number of Shards**: The number of shards in this cluster. A shard is a partition of your data and is comprised of one primary and up to five read replica
- **Read** Scaling using **Read Replicas**
  - **Replicas per Shard**: Input number of replicas per shard between 0 and 5. Zero replicas will not enable an enhanced cluster with primary/replica roles.

## 1.2 Single Redis Cluster
- can have read replicas, BUT no Sharding


![](https://i.loli.net/2019/08/21/IiNJsvkqc1nyFw3.png)


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


























