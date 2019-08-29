
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
