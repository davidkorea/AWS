# 1. Metadata
- 实例的metadata是关于实例本身的数据，这些数据描述了实例的一些状态和基本信息
- metadata包括的信息有实例的ID，使用的AMI ID，系统名，公网IP地址，内网IP地址，关联的角色等等
  - 可以通过http://169.254.169.254/latest/meta-data 来获取metadata的信息
- Metadata可以用来管理和配置实例
  - 如访问http://169.254.169.254/latest/meta-data/public-ipv4 就可以获得EC2实例的公网IP地址
    ```sh
    [root@ip-172-31-26-251 ~]# curl http://169.254.169.254/latest/meta-data/public-ipv4
    13.125.251.19
    [root@ip-172-31-26-251 ~]#
    ```
# 2. User Data
- User data可以用来在EC2实例启动的时候作为启动脚本的内容（详细可以看上一节内容Bootstrap开机脚本）
- User data也可以在实例启动之后用于执行一些批量/自动化的脚本
- 可以通过http://169.254.169.254/latest/user-data 即可以获得实例的user data
  ```sh
  [root@ip-172-31-26-251 ~]# http://169.254.169.254/latest/user-data
  -bash: http://169.254.169.254/latest/user-data: No such file or directory
  ```
  ![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/04/ec2-user-data.jpg)
  
**URL地址的IP固定，只能在EC2中访问**
