# Aurora简介
Amazon Aurora是一种兼容MySQL和PostgreSQL的商用级别关系数据库，它既有商用数据库的性能和可用性（比如Oracle数据库），又具有开源数据库的成本效益（比如MySQL数据库）。

Aurora的速度可以达到MySQL数据库的5倍，同时它的成本只是商用数据库的1/10。

Aurora和其他RDS服务类似，AWS会负责各种管理任务，例如硬件、数据库补丁和数据库备份等。

另外，Aurora还有以下这些特点：

- 10GB的起始存储空间，可以增加到最大64TB的容量
- 计算资源可以提升到最多32vCPU和244GB的内存
- Aurora会将你的数据复制2份到每一个可用区内，并且复制到最少3个可用区，因此你会有6份数据库备份
  - 和S3一样，我们接触的endpoint也只是一个，但是后台Aurora会将数据复制到3个可用区，这对于我们来说是不透明的。这些数据是同步，非异步的。
- 2份及以下的数据备份丢失，不影响Aurora的写入功能
- 3份及以下的数据备份丢失，不影响Aurora的读取功能
- Aurora有自动修复的功能，AWS会自动检查磁盘错误和数据块问题并且自动进行修复
- 有两种数据库只读副本
  - Aurora Replicas（最多支持15个）
  - MySQL Replica（最多支持5个）
  - 两者的区别是Aurora主数据库出现故障的时候，Aurora Replicas可以自动变成主数据库，而MySQL Replica不可以

- 可以将Aurora的快照分享给其他AWS账户

在助理级别的AWS考试中，不会对Aurora的内容做太深入的考察，我们只需要大致了解即可。



![](https://i.loli.net/2019/07/16/5d2d5c1da7b5699671.png)



# 1.Aurora
![](https://i.loli.net/2019/07/16/5d2d5d4e2cb4329078.png)
![](https://i.loli.net/2019/07/16/5d2d5d5082bc448453.png)
![](https://i.loli.net/2019/07/16/5d2d5d52aedeb68139.png)
![](https://i.loli.net/2019/07/16/5d2d5fe3cb4fd40681.png)

## 1.1 Reader Replica

![](https://i.loli.net/2019/07/16/5d2d5edd7d78d47989.png)
![](https://i.loli.net/2019/07/16/5d2d5f7b6143567800.png)

只读副本并没有终端节点
![](https://i.loli.net/2019/07/16/5d2d60e8b59a417665.png)

## 1.2 Cross-Region Reader Replica

![](https://i.loli.net/2019/07/16/5d2d5eeb11c6853671.png)

# Aurora Serverless
![](https://i.loli.net/2019/07/16/5d2d5c29d255035342.png)
![](https://i.loli.net/2019/07/16/5d2d5c310330c25226.png)
![](https://i.loli.net/2019/07/16/5d2d5c33e9d9d65288.png)
![](https://i.loli.net/2019/07/16/5d2d5c36d7e2b35741.png)
![](https://i.loli.net/2019/07/16/5d2d5c3ab222a91229.png)
![](https://i.loli.net/2019/07/16/5d2d5c3d59f1034773.png)
