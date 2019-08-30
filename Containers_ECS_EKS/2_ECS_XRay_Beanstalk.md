

# 1. ECS + Elastic Beanstalk
- You can run Elastic Beanstalk **in** Single & Multi Docker Container mode
- Multi Docker helps run multiple containers per EC2 instance in EB
- This will create for you:
  - ECS Cluster
  - EC2 instances, configured to use the ECS Cluster
  - Load Balancer (in high availability mode)
  - Task definitions and execution
- Requires a config file **Dockerrun.aws.json** at the **root of source code**
- Your Docker images must be pre-built and stored in ECR for example

![image](http://wx1.sinaimg.cn/large/006gDTsUgy1g6hqar58g2j30r00cijt4.jpg)

![image](http://wx4.sinaimg.cn/large/006gDTsUgy1g6hqp7qoduj30ur0ifjtd.jpg)

















# 2. ECS + X-Ray integration
![image](http://ws4.sinaimg.cn/large/006gDTsUgy1g6hq8cp31xj30y20cnacm.jpg)

- ECS does integrate with X-Ray
  - To make it work, X-Ray must be running as a 2nd container within the task definition
  - You can use the ready-to-use AWS image: https://hub.docker.com/r/amazon/awsxray-daemon/
  - More information here: https://docs.aws.amazon.com/xray/latest/devguide/xraydaemon-ecs.html
- ECS does integrate with CloudWatch Logs:
  - You need to setup logging at the task definition level
  - Each container will have a different log stream
