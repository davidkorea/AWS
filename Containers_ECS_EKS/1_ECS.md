
![](http://ws1.sinaimg.cn/large/006gDTsUgy1g6fh22582lj30ks088mxh.jpg)


|Elastic Container Service (ECS)| Description|
|-|-|
|Cluster| Logical grouping of EC2 instances|
|Container instance| EC2 instance running the the ECS agent|
|Task Definition| Blueprint that describes how a docker container should launch|
|Task| A running container created by using the settings in a Task Definition|
|Service| Defines long running tasks – can control task count with Auto Scaling and attach an ELB|

# 1. Cluster

- ECS 会同时创建**自动伸缩组ASG**和l**aunch configuration**
  ![image](http://ws4.sinaimg.cn/large/006gDTsUgy1g6gbk6n5v1j311y0dbt9s.jpg)
  ![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6gbq5nafcj311y0hkq4t.jpg)
  ![image](http://ws1.sinaimg.cn/large/006gDTsUgy1g6gc0n0wvdj30z108aab3.jpg)


# 2. ECS Task Definitions
- Task 相当于**kubunetes中的Pod**，每个Pod中可以运行多个docker。ECS Task中也可以运行多个docker
- Tasks definitions are **metadata in JSON** form to tell ECS **how to run a Docker Container**
- It contains crucial information around:
  - Image Name
  - Port Binding for Container and Host
  - Memory and CPU required
  - Environment variables
  - Networking information
  - IAM Role
  - Logging configuration (ex CloudWatch)

![image](http://ws3.sinaimg.cn/large/006gDTsUgy1g6gbulsr1gj311i0c13zs.jpg)
![](http://ws4.sinaimg.cn/large/006gDTsUgy1g6gccam655j311y1vpgqv.jpg)
![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6gc4dfp2aj311y0lcq50.jpg)


# 3. ECS Service
- ECS Services help define **how many tasks** should run and how they should be run
- They ensure that the number of tasks desired is running across our fleet of EC2 instances.
- They can be linked to **ELB / NLB / ALB** if needed


















































































