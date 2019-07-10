# Load Balancers

## 1. EC2 Instances
- ap-northeast-2a
  - 13.125.177.84

- ap-northeast-2c
  - 54.180.105.66

## 2. Classic Load Balance
- NO **Target Group** for classicLB

![](https://i.loli.net/2019/07/10/5d257e7e7edfb39674.png)
![](https://i.loli.net/2019/07/10/5d257e8128ebc54915.png)
![](https://i.loli.net/2019/07/10/5d257e846b5f790285.png)
![](https://i.loli.net/2019/07/10/5d257e86cb5f645553.png)

- when one EC2 down, health check will find that and will not route traffic to the instance

![](https://i.loli.net/2019/07/10/5d257fc8186c435664.png)

## 3. ALB
### 3.1 Target Groups

you can have a target group for EU users, you can have another target group for Asian users due to langauge settings.

Your load balancer routes requests to the targets(EC2) in a target group using the target group settings that you specify, and performs health checks on the targets using the health check settings that you specify.

- **Target Type**: 
  - Instance: web server on AWS
  - IP: web servers are not in AWS but have public IP
  - Lambda Function
    
![](https://i.loli.net/2019/07/10/5d2581eed959d89622.png)
![](https://i.loli.net/2019/07/10/5d2581f0b763a70142.png)
