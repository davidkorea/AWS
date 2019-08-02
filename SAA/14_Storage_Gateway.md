# Storage Gateway简介
AWS Storage Gateway 是一种具有无缝本地集成和优化数据传输的混合云存储方案。

你本地数据中心内的服务器可以通过 AWS Storage Gateway 连接访问 Amazon S3、Amazon Glacier、Amazon EBS 等 AWS 存储服务来进行备份、存档、灾难恢复、数据迁移等等。

事实上，AWS Storage Gateway 是一个安装在Vmware Esxi，Microsoft Hyper-V或者EC2上的虚拟机。并不是什么实体的设备。

（2018年9月份更新：现在AWS已经发布了基于实体硬件的AWS Storage Gateway，它是一个基于Dell EMC Poweredge R640的机架式服务器）

知识点
- 文件网关（File Gateway）：通过 NFS 连接直接访问存储在 Amazon S3 或者 Amazon Glacier上的文件，并且本地进行缓存
- Volume Gateway：使用 iSCSI 作为本地磁盘连接到本地服务器上，让本地服务器可以访问到 Amazon S3 内的文件，其中，Volume Gateway 又分为以下两种
  - Stored Volumes：所有的数据都将保存到本地，但是会异步地将数据备份到AWS S3上
  - Cached Volumes：所有的数据都会保存到S3，但是会将最经常访问的数据缓存到本地
- Tape Gateway：用来取代传统的磁带备份，通过 Tape Gateway 可以使用NetBackup，Backup Exec或Veeam 等备份软件将文件备份到 Amazon S3 或者 Amazon Glacier 上
- AWS Storage Gateway 支持三种存储接口：文件、卷和磁带

备注：参加旧版本考试的朋友，可能会遇到关于 Storage Gateway 的问题，但其中会使用旧的名称。Stored Volumes 旧名称是 Gateway Stored Volumes；Cached Volumes 旧名称是 Gateway Cached Volumes, Tape Gateway 旧名称是 Gateway Virtual Tape Library (VTL)。


![](https://i.loli.net/2019/08/02/5d43edc89750a72877.png)

![](https://i.loli.net/2019/06/16/5d0604fc49f9f96817.png)
