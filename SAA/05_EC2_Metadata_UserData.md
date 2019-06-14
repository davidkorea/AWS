# 1. Metadata
- 实例的metadata是关于实例本身的数据，这些数据描述了实例的一些状态和基本信息
- metadata包括的信息有实例的ID，使用的AMI ID，系统名，公网IP地址，内网IP地址，关联的角色等等
  - 可以通过http://169.254.169.254/latest/meta-data来获取metadata的信息
- Metadata可以用来管理和配置实例
  - 如访问http://169.254.169.254/latest/meta-data/public-ipv4就可以获得EC2实例的公网IP地址
    ```sh
    [root@ip-172-31-26-251 ~]# curl http://169.254.169.254/latest/meta-data/public-ipv4
    13.125.251.19
    [root@ip-172-31-26-251 ~]#
    ```
