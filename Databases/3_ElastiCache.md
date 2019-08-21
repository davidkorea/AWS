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
