
## 联机事务处理OLTP（Online Transaction Processing）
OLTP是传统的关系数据库的主要应用，是基本的日常事务处理，例如银行交易等。

OLTP包括了以上所说的关系数据库SQL Server，Oracle，MySQL Server，PostgreSQL，Aurora，MariaDB等。

联机分析处理OLAP（Online Analytics Processing）
OLAP是数据仓库（Data Warehousing）系统的主要应用，支持复杂的分析操作，侧重决策支持，并且能提供直观易懂的查询结果。OLAP是用来做商业智能（Business Intelligence）方面的分析的。

## OLAP常用的流行工具是AWS Redshift, Greenplum, Hive等

说了这么多可能大家的理解都还是比较模糊，下面来举一个通俗一点的例子。

如果一个电商在网上卖产品，那么关于产品的信息，用户的信息，交易的信息都可以存放在OLTP类型的关系数据库上。如果用户需要查询产品有关的信息，或者运营者需要查询产品的销量，产品的库存等都可以直接通过读取数据库获取到信息。

但是当电商发展到一定的规模，运营者/管理层需要做更加精细的用户群体分析，比如“20-30岁的男性在过去一年内的购买行为与电商促销活动之间的关系”，那么就要用到数据仓库了。

数据仓库有更好地读取速度和更加便利的分析和查询方式。
