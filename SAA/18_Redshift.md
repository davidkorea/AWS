# Redshift简介
Amazon Redshift是一个快速、功能强大、完全托管的PB级别数据仓库服务。用户可以在刚开始使用几百GB的数据，然后在后期扩容到PB级别的数据容量。



如之前的课程中所说，Redshift是一种联机分析处理OLAP（Online Analytics Processing）的类型，支持复杂的分析操作，侧重决策支持，并且能提供直观易懂的查询结果。

再举个之前提到的例子：

如果一个传统的电商发展到一定的规模，运营者/管理层需要做更加精细的用户群体分析，比如“20-30岁的男性在过去一年内的购买行为与电商促销活动之间的关系”，那么就要用到数据仓库了。

数据仓库在数据库层面和基础架构层面都与联机事务处理OLTP（Online Transaction Processing）不太一样。

Redshift的一些特点：

- 单节点（160Gb）部署模式
- 多节点部署模式
  - 领导节点：管理连接和接收请求
  - 计算节点：存储数据，执行请求和计算任务，最多可以有128个计算节点
- Columnar Data Storage
- Advanced Compression
- Massively Parallel Processing (MPP)
- 目前Redshift**只能部署在一个可用区内**，不能跨可用区或者用类似RDS的高可用配置
  - Redshift是用来产生报告和做商业分析的，并不需要像生产环境一样对可用性有高保证
- 我们可以对Redshift做快照，并且在需要的时候恢复这个快照到另一个可用区

# Redshift安全
- Redshift传输过程中使用SSL加密
- Redshift使用AES-256进行加密
- 默认情况下Redshift帮我们解决了秘钥管理的问题
  - 我们也可以使用自己的秘钥
  - 或者使用AWS Key Management Service (KMS)来管理秘钥
  
对于助理级的AWS考试来说，Amazon Redshift并不需要了解得特别深入，我们只需要知道它能用来做什么，以及它与RDS的区别就可以了。
