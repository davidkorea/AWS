- Fargate is Serverless (no EC2 to manage)
- AWS provisions containers for us and **assigns them ENI**
- Fargate containers are provisioned by the container spec (CPU / RAM)
- Fargate tasks can have IAM Roles to execute actions against AWS

# 1. Create Fargate Cluster

![image](http://wx3.sinaimg.cn/large/006gDTsUgy1g6hjs53g1jj30ya0ktjta.jpg)

# 2. Fargate Task Definition
- Cannot use the Task Definition that created before, cuz that was EC2 type
  ![image](http://ws1.sinaimg.cn/large/006gDTsUgy1g6hjtj7zwqj30ye0cqabg.jpg)


- Fargate Task Definition
  ![FireShot Capture 023 - Amazon ECS - ap-northeast-2.console.aws.amazon.com](http://wx4.sinaimg.cn/large/006gDTsUgy1g6hjzv4auaj311y1udjxy.jpg)

- Container
  ![image](http://wx2.sinaimg.cn/large/006gDTsUgy1g6hjxkk94oj30s30q8din.jpg)



# 3. Fargate Service

![FireShot Capture 024 - Amazon ECS - ap-northeast-2.console.aws.amazon.com](http://wx4.sinaimg.cn/large/006gDTsUgy1g6hk1wiru1j311y1580wm.jpg)

alb error
![FireShot Capture 025 - Amazon ECS - ap-northeast-2.console.aws.amazon.com](http://wx2.sinaimg.cn/large/006gDTsUgy1g6hk3w65u3j311y2amqb5.jpg)

modify alb
![image](http://ws3.sinaimg.cn/large/006gDTsUgy1g6hk54pzv3j311y0crjse.jpg)

service ok
![image](http://wx3.sinaimg.cn/large/006gDTsUgy1g6hk7pex8xj30td0pv0ux.jpg)

![image](http://ws4.sinaimg.cn/large/006gDTsUgy1g6hkdeun1vj311y0if0vl.jpg)

alb ok
![image](http://ws3.sinaimg.cn/large/006gDTsUgy1g6hkbh046zj30mx0hrjse.jpg)
