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

# EFS挂载方式
1. Amazon EC2 挂载说明 (从本地 VPC)
    - 使用 Amazon EC2 控制台，将您的 EC2 实例与支持访问您的挂载目标的 VPC 安全组关联。例如，如果您向您的挂载目标(EFS)分配了“默认”安全组，则应将“默认”安全组分配到您的 EC2 实例。
    - Using the Amazon EC2 console, associate your EC2 instance with a VPC security group that enables access to your mount target. For example, if you assigned the "default" security group to your mount target, you should assign the "default" security group to your EC2 instance.
    - 挂载您的文件系统
    ```
    # 在 EC2 实例上创建新目录，如“efs”。
    sudo mkdir efs
    sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-27c34546.efs.ap-northeast-2.amazonaws.com:/ efs
    ```
2. Amazon EC2 挂载说明 (跨 VPC 对等连接)
3. 本地挂载说明
    - 使用AWS Direct Connect连接或基于AWS的VPN连接在本地服务器上挂载EFS文件系统
