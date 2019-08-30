
![](http://ws1.sinaimg.cn/large/006gDTsUgy1g6fh22582lj30ks088mxh.jpg)


|Elastic Container Service (ECS)| Description|
|-|-|
|Cluster| Logical grouping of EC2 instances|
|Container instance| EC2 instance running the the ECS agent|
|Task Definition| Blueprint that describes how a docker container should launch|
|Task| A running container created by using the settings in a Task Definition|
|Service| Defines long running tasks – can control task count with Auto Scaling and attach an ELB|

# 1. Cluster

- ECS 会同时创建**自动伸缩组ASG**和**Launch Configuration**
  ![image](http://ws4.sinaimg.cn/large/006gDTsUgy1g6gbk6n5v1j311y0dbt9s.jpg)
  ![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6gbq5nafcj311y0hkq4t.jpg)
  ![image](http://ws1.sinaimg.cn/large/006gDTsUgy1g6gc0n0wvdj30z108aab3.jpg)

- User data in **Launch Configuration**
  ![image](http://wx1.sinaimg.cn/large/006gDTsUgy1g6hp9f6k7ej311y0gljts.jpg)
  ```
  [root@ip-172-31-4-201 ~]# cat /etc/ecs/ecs.config 
  ECS_CLUSTER=my-ecs-cluster
  ECS_BACKEND_HOST=
  ```
  
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

## 3.1 Service without ELB
- cannot set ELB after cluster created

![](http://wx3.sinaimg.cn/large/006gDTsUgy1g6gdehulhcj30zm18341r.jpg)

task runs in a service 
![](http://wx3.sinaimg.cn/large/006gDTsUgy1g6gghs4gn9j30z40xujtw.jpg)

### 3.1.1 Get access to httpd 
- Add 8080 for `0.0.0.0/0` in SG to allow docker httpd access

```
[root@ip-172-31-6-149 ~]# docker ps
CONTAINER ID        IMAGE                            COMMAND              CREATED             STATUS              PORTS                  NAMES
ccfa55fbe8cb        httpd:2.4                        "httpd-foreground"   36 minutes ago      Up 36 minutes       0.0.0.0:8080->80/tcp   ecs-httpd-1-httpd-ec96f186accea2cde501
27369d459888        amazon/amazon-ecs-agent:latest   "/agent"             2 hours ago         Up 2 hours          ecs-agent

[root@ip-172-31-6-149 ~]# curl localhost:8080
<html><body><h1>It works!</h1></body></html>
```

### 3.1.2 Scale
- cannot scale by **service** to expand **task number**
  - because **Host 80 Port** has been used by the first docker in the first **task**
  
  ![image](http://ws4.sinaimg.cn/large/006gDTsUgy1g6gekvytsxj30tz06jaau.jpg)
  ![image](http://wx1.sinaimg.cn/large/006gDTsUgy1g6gelyopqfj30u30bc75l.jpg)

- **add new EC2** instances by **scale ecs cluster**, let each EC2 run a Task with one dokcer httpd,  New EC2 will be added to ASG
  - **Service** will run on each EC2 in the cluster
  ![image](http://wx3.sinaimg.cn/large/006gDTsUgy1g6geq7hfzyj311y0jo41g.jpg)
  ![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6geub6rj4j30tz094t9v.jpg)
  ![image](http://wx3.sinaimg.cn/large/006gDTsUgy1g6ggk0s394j310h0fgdhh.jpg)



## 3.2 Service with ALB
- Create new revision of **Task Definition** with docker container map **dynamic Host port** to **docker 80 port**
- create a new **Service**
  - create a new BLANK ALB

![](http://ws1.sinaimg.cn/mw690/006gDTsUgy1g6glxhb8p8j30kw0f2jto.jpg)

### 3.2.1 Update Task Definition
![image](http://wx2.sinaimg.cn/large/006gDTsUgy1g6gj15gub2j30ve0lcdi6.jpg)
![image](http://wx1.sinaimg.cn/large/006gDTsUgy1g6gj24mgmaj311y0hmdhr.jpg)

### 3.2.2 Create new Service
- create new Service for the cluster, select the **new version of Task**
  ![image](http://ws2.sinaimg.cn/large/006gDTsUgy1g6gj3zt2mmj30ne0hmab0.jpg)
  
- Create a BLANK ALB manually
  - **Name**	``my-ecs-cluster-elb``
  - **VPC, Subnets**	
  - **Security groups**	``ecs-alb`` woth 80 port open
  - **Target group name**	``ecs-tg``
  - leave all others default
- Add ALB for Service
  ![image](http://wx3.sinaimg.cn/large/006gDTsUgy1g6gjcgvfsjj30ox0hm75z.jpg)  
  ![image](http://ws3.sinaimg.cn/large/006gDTsUgy1g6gjtq0118j30ry0hmjsr.jpg)  

  - Desigired 4 but running 2, check Service Events, EC2 has no enough memory to run more dockers
    ![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6gkvhqjtxj30uk0hm76g.jpg)
    ![image](http://ws2.sinaimg.cn/large/006gDTsUgy1g6gkywigcnj30u1088gml.jpg)
    - can scale the cluster by adding more EC2s, and task will be expanded as desigred number
      ![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6glszywt5j30u5078t9i.jpg)
    
- Allow all Port for ALB in the EC2 SG. because each docker 80 port will map to a random tcp port on the Host EC2
  ![image](http://ws3.sinaimg.cn/large/006gDTsUgy1g6gj9m9iq9j311y0e1di8.jpg)

  - If NOT allowed Host EC2 random port `32772` and `32773`, errer log
  
  ![image](http://ws4.sinaimg.cn/large/006gDTsUgy1g6hh4i4b1dj30u10kln0s.jpg)









































































































































