# Elasticache简介
Elasticache是AWS提供的分布式内存对象缓存系统，可以有效地提升现有应用程序的性能。利用Elasticache，用户可以从高吞吐和低延迟的内存数据存储中检索数据。

Elasticache通过在内存中缓存数据来减少对象读取数据库的次数，减轻了数据库的负载，以及提高了网站的访问速度（内存的访问速度比磁盘的访问速度高很多）。一般来说我们会把相对来说更新频繁的“热数据”放在Elasticache中，把“冷数据”还是放在数据库中，以支持及时的更新。

重点：Elasticache是存储在内存中的，是一种in-memory cache系统。

目前Elasticache支持两种业界流行的引擎，分别是：

- Memcached
  - Memcached是一套高性能、分布式内存对象缓存系统，是一款开源的，非常流行的缓存系统
  - 使用AWS Elasticache可以和Memcached无缝地兼容
- Redis

在实际场景中，如果我们有对数据库的读写有很高的要求，并且数据的更新没有那么频繁，我们就可以考虑使用Elasticache来减少我们的数据库负担，增加数据库读取的性能。



当然，我们也可以使用上一章节将的Read Replicas来解决同样的事情。但不同的是**Elasticache是缓存数据库的内容**，**Read Replicas会异步地同步数据库的内容**。另一个不同是，Elasticache是存储在内存中的，因此比起构建在SSD的Read Replicas会快不止一个数量级。

同样的，Elasticache这部分内容在助理级解决方案架构师的考试中只需要稍作了解即可，但是在助理级开发者考试中会需要有更加深入的掌握。

![](https://i.loli.net/2019/07/16/5d2d6743860d058732.png)
