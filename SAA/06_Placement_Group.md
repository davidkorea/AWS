# EC2置放群组（Placement Group）
Placement Group的特点
- ~~EC2 Placement Group不能跨越多个可用区（这是2017年11月之前的特性）~~
- EC2 Placement Group分为集群模式（Cluster）和分布模式（Spread）
  - 集群模式（Cluster）即传统的置放群组，所有的实例需要在同一个可用区内
    -  将一个可用区中靠近的实例打包在一起。通过使用该策略，工作负载可以实现所需的低延迟网络性能，以满足 HPC 应用程序通常使用的紧密耦合的节点到节点通信的要求
  - 分布模式（Spread）是将实例分布到不同的底层硬件，**可以在不同的可用区内**，同一个region即可。你最多可以在每一个置放群组的每一个可用区内创建7个实例
- Placement Group提供了低延迟，高速率的网络，可提供高达10 Gbps的速度
- EC2 Placement Group的命名需要在你的AWS账户内唯一，不能有命名重复
- 只有**特定的EC2实例类型可以放在配置Placement Group内（某些计算优化型、GPU、内存优化型和存储优化型的实例）**
    - 通用型：A1、M4、M5、M5a、M5ad 和 M5d
    - 计算优化型：C3、C4、C5、C5d、C5n 和 cc2.8xlarge
    - 内存优化型：cr1.8xlarge、R3、R4、R5、R5a、R5ad、R5d、X1、X1e 和 z1d
    - 存储优化型：D2、H1、hs1.8xlarge、I2、I3 和 I3en
    - 加速计算型：F1、G2、G3、P2 和 P3
  - 所以在创建t2.micro实例时，无法选择cluster放置组，但是可以选择spread防止组。
    ![](https://i.loli.net/2019/08/01/5d42872962dd192724.png)
    
- AWS建议在一个Placement Group内的所有EC2实例是一模一样的，否则会有短板效应
- **不可以合并多个EC2 Placement Group**
- **不可以将一个正在运行的EC2实例放到一个EC2 Placement Group中；只能为这个EC2实例创建一个AMI，然后基于AMI创建一个新的实例并且加入到Placement Group内**
  - 用CLI命令行可以改（[将实例移至置放群组（命令行）](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/placement-groups.html?shortFooter=true#change-instance-placement-group)），但目前console不可以改，console只有在创建的时候可以选择，一旦创建好了，是没办法改的
    ![](https://i.loli.net/2019/06/14/5d03449d5798328318.png)
    
- Placement Group**可以跨越peerd VPC，但要保证在同一个可用区内**
  - VPC Peering的功能，让不同VPC之间能够通信。Placement Group能够跨越不同的VPC，哪怕是自己的账号与其他人的账号
  - 意思就是可用跨VPC，但是其他VPC中的实例所在的AZ可用区要一致？？
- 如果在Placement Group中创建实例的时候出现“capacity error”的错误，可以停止再启动组中的所有实例，再重新创建刚才的实例
  - 停止再启动组中的所有实例可以改变这些实例所在的底层物理设备，从而带来更多的性能和空间启动新的实例
- Placement Group的创建会告诉AWS将组里的实例安置在物理上接近的AWS设备内
# 使用Placement Group的最佳实践
- 组内使用一样类型的EC2实例
- 在同一时间启动组内所有EC2实例，这样可以减少出现“capacity error”错误的概率
- 可以通过更改最大传输单元（MTU），从默认的1500改成9001来进一步增加Placement Group内实例之间的传输速度

# Exam Tips
![](https://i.loli.net/2019/06/16/5d0518d5cf96839208.png)


