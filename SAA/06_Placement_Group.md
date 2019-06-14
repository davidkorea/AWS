# EC2置放群组（Placement Group）
Placement Group的特点
- ~~EC2 Placement Group不能跨越多个可用区（这是2017年11月之前的特性）~~
- EC2 Placement Group分为集群模式（Cluster）和分布模式（Spread）
  - 集群模式（Cluster）即传统的置放群组，所有的实例需要在同一个可用区内
    -  将一个可用区中靠近的实例打包在一起。通过使用该策略，工作负载可以实现所需的低延迟网络性能，以满足 HPC 应用程序通常使用的紧密耦合的节点到节点通信的要求
  - 分布模式（Spread）是将实例分布到不同的底层硬件，可以在不同的可用区内。你最多可以在每一个置放群组的每一个可用区内创建7个实例
- Placement Group提供了低延迟，高速率的网络，可提供高达10 Gbps的速度
- EC2 Placement Group的命名需要在你的AWS账户内唯一，不能有命名重复
- 只有**特定的EC2实例类型可以放在配置Placement Group内（某些计算优化型、GPU、内存优化型和存储优化型的实例）**
- AWS建议在一个Placement Group内的所有EC2实例是一模一样的，否则会有短板效应
- **不可以合并多个EC2 Placement Group**
- **不可以将一个正在运行的EC2实例放到一个EC2 Placement Group中；只能为这个EC2实例创建一个AMI，然后基于AMI创建一个新的实例并且加入到Placement Group内**
- Placement Grou**p可以跨越peerd VPC，但要保证在同一个可用区内**
- 如果在Placement Group中创建实例的时候出现“capacity error”的错误，可以停止再启动组中的所有实例，再重新创建刚才的实例
  - 停止再启动组中的所有实例可以改变这些实例所在的底层物理设备，从而带来更多的性能和空间启动新的实例
- Placement Group的创建会告诉AWS将组里的实例安置在物理上接近的AWS设备内
# 使用Placement Group的最佳实践
- 组内使用一样类型的EC2实例
- 在同一时间启动组内所有EC2实例，这样可以减少出现“capacity error”错误的概率
- 可以通过更改最大传输单元（MTU），从默认的1500改成9001来进一步增加Placement Group内实例之间的传输速度
