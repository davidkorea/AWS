
**In RDS, changes to the backup window take effect ________. -- immediately** 

**Can I "force" a failover for any RDS instance that has Multi-AZ configured? -- YES**

![](https://i.loli.net/2019/07/16/5d2d6a615e8ba31558.png)


### 联机事务处理OLTP（Online Transaction Processing）
OLTP是传统的关系数据库的主要应用，是基本的日常事务处理，例如银行交易等。

OLTP包括了以上所说的关系数据库SQL Server，Oracle，MySQL Server，PostgreSQL，Aurora，MariaDB等。

### 联机分析处理OLAP（Online Analytics Processing）
OLAP是数据仓库（Data Warehousing）系统的主要应用，支持复杂的分析操作，侧重决策支持，并且能提供直观易懂的查询结果。OLAP是用来做商业智能（Business Intelligence）方面的分析的。

OLAP常用的流行工具是AWS Redshift, Greenplum, Hive等

说了这么多可能大家的理解都还是比较模糊，下面来举一个通俗一点的例子。

如果一个电商在网上卖产品，那么关于产品的信息，用户的信息，交易的信息都可以存放在OLTP类型的关系数据库上。如果用户需要查询产品有关的信息，或者运营者需要查询产品的销量，产品的库存等都可以直接通过读取数据库获取到信息。

但是当电商发展到一定的规模，运营者/管理层需要做更加精细的用户群体分析，比如“20-30岁的男性在过去一年内的购买行为与电商促销活动之间的关系”，那么就要用到数据仓库了。

数据仓库有更好地读取速度和更加便利的分析和查询方式。

# 1. RDS的备份
本章节主要讲解RDS数据库的备份，高可用（Multi-AZ）和只读副本（Read Replicas）的特性以及他们的区别。

AWS RDS提供了两种不同的备份方式，分别是自动备份（Automated Backups）和快照（Snapshots）。

### 1. 自动备份（Automated Backups）
- 你可以在创建数据库的时候定义自动备份的保留时间（Retention Period），这个时间的设置范围是1天~35天
- 你也可以在创建数据库之后更改这个保留时间（Retention Period）
- 如果需要，你可以将数据库恢复到保留时间内的任何时间点
- 在你删除数据库的时候，所有的自动备份都会被删除
- RDS的自动备份会保存在Simple Storage Service (S3)上
- 我们可以定义自动备份的时段，在这个备份时段内数据库将会自动进行备份
- **在自动备份的过程中，数据库存储的I/O可能会暂停（通常不到几秒），数据库性能会降低，但部署了Multi-AZ的数据库不受影响**
### 2. 快照（Snapshots）
- RDS的快照需要手动进行
- 在你删除数据库的时候，快照不会被删除，不像自动备份那样
- **在创建快照的过程中，数据库存储的I/O可能会暂停（通常不到几秒），数据库性能会降低，但部署了Multi-AZ的数据库不受影响**
  - **What happens to the I/O operations of a single-AZ RDS instance during a database snapshot or backup?**
    - I/O may be briefly suspended while the backup process initializes (typically under a few seconds), and you may experience a brief period of elevated latency.

## 数据库加密
现在AWS RDS的**所有6种关系数据库都**支持加密。一旦启用了加密的功能，所有数据的存储都将会被加密，包括数据库本身、自动备份、快照和只读副本（read replicas）。

- 如果在创建数据库的时候没有加密，我们不能在事后对其进行加密
- 但我们可以创建这个数据库的快照，复制该快照并且加密这个复制的版本
# 2. Multi-AZ高可用
我们可以把AWS RDS数据库部署在多个可用区（AZ）内，以提供高可用性和故障转移支持。

使用Multi-AZ部署模式，RDS会在不同的可用区内配置和维护**一个主数据库**和**一个备用数据库**，主数据库的数据会自动复制到备用数据库中。

使用这种部署模式，可以为我们提供数据冗余，减少在系统备份期间的I/O冻结（上面有提到）。同时，更重要的是可以防止数据库实例的故障和单个可用区的故障。

如下图所示，我们可以在两个可用区内分别部署主数据库和备用数据库。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/07/con-multi-AZ.png)

目前Multi-AZ支持以下数据库：**（全部支持多AZ）**

- Oracle
- PostgreSQL
- MySQL
- MariaDB
- SQL Server

值得注意的是，Aurora数据库本身就支持多可用区部署的高可用设置，因此不需要为Aurora数据库特别开启这个功能。

在上次实验中我们有讲到，创建了RDS数据库之后我们会得到一个数据库的URL Endpoint。在开启Multi-AZ的情况下，这个URL Endpoints会根据主/备数据库的健康状态自动解析到IP地址。对于应用程序来说，我们只需要连接这个URL地址即可。

**高可用的设置只是用来解决灾备的问题，并不能解决读取性能的问题；要提升数据库读取性能，我们需要用到Read Replicas**

# 3. 只读副本（Read Replicas）
我们可以在源数据库实例的基础上，复制一种新类型的数据库实例，称之为只读副本（Read Replicas）。我们对源数据库的任何更新，都会异步更新到只读副本中。

因此，我们可以将应用程序的数据库读取功能转移到Read Replicas上，来减轻源数据库的负载。

对于有大量读取需求的数据库，我们可以使用这种方式来进行灵活的数据库扩展，同时突破单个数据库实例的性能限制。

Read Replicas还有如下的特点：

- Read Replicas是用来提高读取性能的，不是用来做灾备的
  - 因为Read Replicas默认情况下只能读不能写，不能作为自动灾备的方案。当然可以手动提升为独立数据库，但一般如果涉及手动，也可以从数据库快照来创建一个独立数据库，这样的方法还有，但都不是**自动的灾备方案**
  - **灾备是指能自动实现的**
- 要创建Read Replicas，**需要源RDS实例开启了自动备份**的功能
- 可以为数据库创建最多5个Read Replicas
- 可以为Read Replicas创建Read Replicas（如下图所示）
- 每一个Read Replicas都有自己的URL Endpoint
- 可以为一个启用了Multi-AZ的数据库创建Read Replicas
  - Read Replicas也可以创建完Multi-AZ
- Read Replicas可以提升成为独立的数据库
- 可以创建位于另一个区域（Region）的Read Replicas
- 可以创建Aurora类型的Read Replicas
  - 不能跨区域，此处与mysql readreplica不同
  
![](https://i.loli.net/2019/07/16/5d2d34d8c949351826.png)
![](https://i.loli.net/2019/07/16/5d2d3625bbce977633.png)
![](https://i.loli.net/2019/07/16/5d2d3625cc9ab70104.png)

-----

![](https://i.loli.net/2019/07/16/5d2d3c660f48342592.png)
![](https://i.loli.net/2019/07/16/5d2d3c681305620333.png)


目前Read Replicas支持以下4个数据库：

- Aurora
- PostgreSQL
- MySQL
- MariaDB

**NO**
- ~~Oracle~~
- ~~MS SQL Server~~

# 2. 非关系数据库DynamoDB
DynamoDB是一种非关系数据库（NoSQL），可在任何规模提供可靠的性能。DynamoDB能在任何规模下实现不到10毫秒级的一致相应，并且它的存储空间无限。

DynamoDB的特点：

- **使用SSD存储**
- 数据分散在3个不同地理位置的数据中心（并不是3个可用区）
- 最终一致性读取（Eventual Consistent Reads），**默认的设置**
  - 即如果写入数据到DynamoDB之后马上读取该数据，可能会读取到旧的信息
  - DynamoDB需要时间（一秒内）把写入的数据同步到3个不同地理位置的数据中心
- 强一致性读取（Strongly Consistent Reads）
  - 在写入数据到DynamoDB之后马上读取该数据，会等所有写入操作以及数据同步全部完成后再回馈结果
  - 即强一致性读取一定会读到最新的数据结果
- 如果我们需要增加DynamoDB的规格，我们可以直接在AWS管理控制台上进行更改，并且不会有任何系统downtime
- 除非您指定其他读取方式，否则 DynamoDB 将使用最终一致性读取。读取操作 (例如 GetItem，Query 和 Scan) 提供了一个 ConsistentRead 参数。如果您将此参数设置为 true，DynamoDB 将在操作过程中使用强一致性读取。


![](https://i.loli.net/2019/07/16/5d2d7bbf13fe755625.png)

![](https://i.loli.net/2019/07/16/5d2d392847f2f31125.png)

![](https://i.loli.net/2019/07/16/5d2d392819b1356150.png)
![](https://i.loli.net/2019/07/16/5d2d39282b6cf21575.png)







