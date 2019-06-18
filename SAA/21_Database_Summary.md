# 数据库总结
- RDS – 联机事务处理OLTP（Online Transaction Processing），包括：
  - SQL Server
  - Oracle
  - MySQL Server
  - PostgreSQL
  - Aurora
  - MariaDB
- 非关系数据库DynamoDB
- 数据仓库RedShift – 联机分析处理OLAP（Online Analytics Processing）
- Elasticache – 内存缓存，支持的引擎
  - Memcached
  - Redis
![](https://i.loli.net/2019/06/18/5d089f761f36486298.png)

# Multi-AZ高可用
使用Multi-AZ部署模式，RDS会在不同的可用区内配置和维护一个主数据库和一个备用数据库，主数据库的数据会自动复制到备用数据库中。

在开启Multi-AZ的情况下，这个URL Endpoints会根据主/备数据库的健康状态自动解析到IP地址。对于应用程序来说，我们只需要连接这个URL地址即可。

高可用的设置只是用来解决灾备的问题，并不能解决读取性能的问题；要提升数据库读取性能，我们需要用到Read Replicas。

# 只读副本（Read Replicas）
我们可以在源数据库实例的基础上，复制一种新类型的数据库实例，称之为只读副本（Read Replicas）。我们对源数据库的任何更新，都会异步更新到只读副本中。
因此，我们可以将应用程序的数据库读取功能转移到Read Replicas上，来减轻源数据库的负载。
- 需要源RDS实例开启了自动备份的功能
- 所有类型RDS都可以有Aurora和mysql两种类型read replica？？？
  - NO, ONLY Aurora has 2 types of read replicas
  - Mysql RDS 只能创建Aurora Read Replica
    ![](https://i.loli.net/2019/06/18/5d08a1bd403ff49770.png)
    
![](https://i.loli.net/2019/06/18/5d089fa6a087546547.png)

# Aurora
- Aurora会将你的数据复制2份到每一个可用区内，并且复制到最少3个可用区，因此你会有6份数据库备份
- 有两种数据库只读副本
  - Aurora Replicas（最多支持15个）
  - MySQL Replica（最多支持5个）
  - 两者的区别是Aurora主数据库出现故障的时候，Aurora Replicas可以自动变成主数据库，而MySQL Replica不可以
  
![](https://i.loli.net/2019/06/18/5d08a04b5251083057.png)


# DynamoDB
- 使用SSD存储
- 数据分散在3个不同地理位置的数据中心（并不是3个可用区）
- 最终一致性读取（Eventual Consistent Reads）
  - 默认的设置，即如果写入数据到DynamoDB之后马上读取该数据，可能会读取到旧的信息
  - DynamoDB需要时间（一秒内）把写入的数据同步到3个不同地理位置的数据中心
- 强一致性读取（Strongly Consistent Reads）
  - 在写入数据到DynamoDB之后马上读取该数据，会等所有写入操作以及数据同步全部完成后再回馈结果
  - 即强一致性读取一定会读到最新的数据结果
- 如果我们需要增加DynamoDB的规格，我们可以直接在AWS管理控制台上进行更改，并且不会有任何系统downtime

# RedShift
- 单节点（160Gb）部署模式
- 多节点部署模式
  - 领导节点：管理连接和接收请求
  - 计算节点：存储数据，执行请求和计算任务，最多可以有128个计算节点

# Elasticache
Elasticache通过在内存中缓存数据来减少对象读取数据库的次数，减轻了数据库的负载，以及提高了网站的访问速度（内存的访问速度比磁盘的访问速度高很多）。一般来说我们会把相对来说更新频繁的“热数据”放在Elasticache中，把“冷数据”还是放在数据库中，以支持及时的更新。

目前Elasticache支持两种业界流行的引擎，分别是：
- Memcached
  - Memcached是一套高性能、分布式内存对象缓存系统，是一款开源的，非常流行的缓存系统
  - 使用AWS Elasticache可以和Memcached无缝地兼容
- Redis

![](https://i.loli.net/2019/06/18/5d08a0a3bd2b514741.png)


# 官方FAQ
强烈建议研读官方FAQ，可以对AWS RDS的概念和实际使用场景有更深入的了解
https://aws.amazon.com/cn/rds/faqs/
