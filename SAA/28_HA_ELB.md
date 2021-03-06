# Load Balancers

- Application LB
  - intelligent routing
- Network LB
  - high performance
- Classic LB
  - cost efficiency
  - if need you end user ip addr, look for the `X_Forwarded_For` header, 通过X_Forwarded_For header来查看发起请求的client的ip地址

## 0. Advanced Load Balancer theory 
### 0.1. Sticky Sessions（Classic LB ONLY??????????）
- 如果所有流量都到了同一个EC2，而没有到另一个EC2，可能需要disable Sticky Sessions
  ![](https://i.loli.net/2019/06/20/5d0b2c673ff8116691.png)
  ![](https://i.loli.net/2019/07/12/5d2845cf2b71f53371.png)
  
### 0.2. Cross Zone Load balancing（Classic LB ONLY??????????）
- 用户通过Route53，50%到AZ1的LB（4 EC2），50%到AZ2的LB（1EC2），但AZ1的流量占比为12.5，并非100/5EC2=20.
- 需打开跨区域负载均衡，使得AZ1的LB可以将流量发送给AZ2中的实例
- 每一个AZ中的LB都可以将流量发送给其他AZ下LB的实例
  ![](https://i.loli.net/2019/06/20/5d0b2db99a5b849911.png)
  ![](https://i.loli.net/2019/07/12/5d2845cd00be871477.png)
  
### 0.3. Path Patterns (ALB)
- 根据URL路径来分配流量
  ![](https://i.loli.net/2019/06/20/5d0b2e13028a254794.png)
  ![](https://i.loli.net/2019/07/12/5d2846644cc7256818.png)
  
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

- add Targets to Target Groups

![](https://i.loli.net/2019/07/10/5d2582c0ab88515244.png)
![](https://i.loli.net/2019/07/10/5d2582c2bea5468380.png)
![](https://i.loli.net/2019/07/10/5d258479d293543147.png)

也可以不提前创建，在创建ALB时，创建Target Groups

### 3.2 ALB

![](https://i.loli.net/2019/07/10/5d2584968487275806.png)
![](https://i.loli.net/2019/07/10/5d258498abe8a41340.png)
![](https://i.loli.net/2019/07/10/5d25849de1de535062.png)
![](https://i.loli.net/2019/07/10/5d2584a0601cc53171.png)
![](https://i.loli.net/2019/07/10/5d2584a3db42071614.png)

- Listener Rules

![](https://i.loli.net/2019/07/10/5d25857977fb766418.png)
![](https://i.loli.net/2019/07/10/5d25857d3ba2d95191.png)






















