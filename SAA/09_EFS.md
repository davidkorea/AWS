# EFS (Elastic File System)

多个EC2实例上使用Amazon EFS进行数据共享。

Amazon EFS提供了可扩展的文件系统，可以用在EC2实例上。EFS使用起来非常简单，你很容易地就可以在EFS上创建和配置一个文件系统。

它是具有弹性的，会根据文件总量的大小自动伸缩，即它能永远满足你的需求。

你可以在多个EC2实例上使用同样的一个EFS文件系统，以达到共享通用数据的目的。

Amazon EFS可以简单地理解为是共享盘或NAS存储。

EFS的一些特性：

- 支持Network File System version 4 (NFSv4)协议
- EFS是Block Base Storage，而不是Object Base Storage（例如S3）
- 使用EFS，你只需要为你使用的存储空间付费，没有预支费用
- 可以有高达PB级别的存储
- 同一时间能支持上千个NFS连接
- EFS的数据会存储在一个AWS区域的多个可用区内
- Read After Write Consistency
- Amazon EFS在Windows实例上不受支持
