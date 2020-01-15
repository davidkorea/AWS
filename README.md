# AWS

- 资本性支出 CapEX, capital expenses
    - 基础设置，服务器
- 运营性支出 OpeEX, operational expenses



-----

- auto scaling中的CLB和target group（ALB，NLB）是分开的，二选一。如果需要可以同时选择
- 使用auto scaling的话，一个EC2实例可以注册到多个ELB（ALB，CLB）
- create auto scaling in non-default VPC，自定义VPC创建ASG时，需要开启自动分配公有IP，否则无法在自定义VPC创建 [Tutorial: Set Up a Scaled and Load-Balanced Application](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-register-lbs-with-asg.html)
    - Create **Launch Configuration** - Advanced Details - IP Address Type - Assign a public IP address to every instance.
    - Create **launch template** - Network interfaces Info - Auto-assign public IP Info - Enable


-----

- Instance Template + EC2 Fleet(spot + ondemand), Classic LB
    - no ASG, can only use specific capacity in a CLB
- 1 Target group(spot, ondemand) or Instance Template + 1 ASG. ELB
    - 1 sopt targetgroup + 1 ondemand targetgroup => same ASG (o)
        - Instance Template (Combine purchase options and instances) + ASG
        - [New – EC2 Auto Scaling Groups With Multiple Instance Types & Purchase Options](https://aws.amazon.com/cn/blogs/aws/new-ec2-auto-scaling-groups-with-multiple-instance-types-purchase-options/)
        - [Auto Scaling Groups with Multiple Instance Types and Purchase Options](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-purchase-options.html)




-----

"architect for failure". Rather than architect your applications to run on instances which are supposed to run constantly, you should build an architecture that can mitigate instance failures by running multiple instances for executing your code. Rather than having a robust instance per service, have multiple smaller instances per service with the ability to mitigate the impact of failures.

### CCoE - Cloud Center of Excellence
A CCoE is a cross-functional group or team with executive support that lead other employees (and the organisation as a whole) through cloud adoption, migration and operations – and establish repeatable processes and cloud standards for everyone to follow in a cloud-first approach.

https://www.rackspace.com/aws-future-insight/aws-insights/helping-customers-build-aws-cloud-centre-excellence/

-----

《AWS成本优化》

## 安全
1. WAF - sql injection
2. shield - DDOS


## 加密
1. 数据加密 - KMS，对内加密
2. 网站加密ssl - Certificate manager，对外加密
3. IAM - 那些人可以调用MKS来给数据加密和解密


## 安全监控

日志每天必看

1. cloudwatch，流水账，查看系统发生了什么，记录没意见事情
2. cloudtrail，更改一级别的记录，不如watch全面，只记录aws账户的操作。比如哪个小管理员做错了事
3. vpc flow logs，网络传输日志，双向传输抓包器，接口，源ip，协议，目标ip，目标端口















-----

- tommorrow is the first blank page of a 365-page-book, write a good one.
- 365 new days, 365 new excuses.




----=

2019-2020

-----

我看也行 changed

-----

不是不行 enabled

ses, not available in Seoul

-----

- encrypt
    - kms
    - hsm
- authentication，authority，audit


-----

- block store
- object store
- file store


-----

- CLI for sysops
    ```
    aws s3 mb s3://my-bucket
    ```
- SDK API for developers
    ```python
    import boto3
    s3 = boto3.client('s3')
    s3.create_bucket(Bucket='my-bucket')
    ```


-----

py-hadoop

theia

-----

## 超融合

https://blog.csdn.net/qiansg123/article/details/80133935

超融合是一种 IT 基础架构构建方式，其核心思想是使用通用硬件，用软件定义来实现 IT 基础架构的各项服务，包括：计算，存储，灾备，运维管理等，并且这些服务都在统一的平台上。超融合概念包含三个要素：

- 使用通用硬件：具体说就是 x86 服务器，所以如果出现专有硬件的 IT 平台的就不属于超融合。比如，传统集中式存储里的存储控制器就属于专有硬件；
- 软件定义： 即超融合 IT 服务是通过 x86 服务器里运行的软件实现的，如：分布式存储软件实现数据存储服务；相比之下，传统 IT 服务大多是用专有硬件且功能逻辑写死在固件里；
- 统一平台：所有的 IT 服务要在一个平台下，隶属于同一个软件栈，这里区别于以往 IT 服务架构方式，各服务会属于不同的平台；需要特别区分的是，市面上将存储设备，服务器，网络交换机放在一个机柜里整体交付的融合方案，如：VCE Vblock，和超融合完全不同。

通常的做法就是拿开源的项目，如：**OpenStack + 开源分布式存储 (Ceph/GlusterFS…)** 搭一个功能看似大而全的“超融合平台”快速推向市场。可以看到，几乎没有用户最终能够真正的用起来，原因在于这种拼凑的“超融合”产品往往各个服务组件的质量无法过关，在可靠性，稳定性，性能方面问题多多，加上开源社区基本不在这些 厂商的控制之下，所谓的售后支持形同虚设，很难达到一个产品应该拥有的市场准备度。

超融合平台整合了哪些 IT 服务？
1. 分布式存储打破数据孤岛
2. 虚拟化计算同样不可或缺
3. 一个平台管理多个IT服务，运维简化是趋势




-----

https://www.gitpod.io/

-----

- sgg集群 - 集群存储，集群监控zabbix
- hadoop基础电商日志
- 贪心NLP

-----

- kinesis
- EMR

-----

### Google Chrome Secure Shell Extension SSH with .pem
1. `ssh-keygen -y -f mykey.pem > mykey.pub`
2. `mv mykey.pem  mykey`
3. login with the 2 files created above
    ![](https://i.postimg.cc/cC3Xqqb7/image.png)
    
-----

### VPC

**Dedicate tenancy** revert to **default tenancy**:
- Once a VPC is set to Dedicated hosting, it can be changed back to default hosting via the **CLI, SDK or API**
- Note that this will **NOT change** hosting settings for **existing instances**, **ONLY future ones**
- **Existing instances** can be **changed** via **CLI, SDK or API** but need to be **in a stopped state** to do so

VPN:
- a private subnet
- Hardware VPN Access
- VPG
- on-premise Customer Gateway.


**VPC Flow Log** can be created at the following levels:
- VPC level
- Subnet level
- Network Interface level













-----

ms terminal https://github.com/microsoft/terminal

-----

- **stop， restart EC2 instance will not lose Private IPv4.**
    ![](https://i.loli.net/2019/08/10/fDc3owzHSvWJByP.png)
    
-----



```
AWS Certified Cloud Practitioner - Practice (Chinese Mandarin Simplified) 

......您好：

感谢您参加AWS Certified Cloud Practitioner - Practice 考试。请检查以下信息，以确定可能需要额外准备哪些主题。

总得分： 100%

主题平均得分：
1.0  Cloud Concepts: 100%
2.0  Security: 100%
3.0  Technology: 100%
4.0  Billing and Pricing: 100%

考试指南

点击此处 获得有关 AWS Certified Cloud Practitioner 考试的更多信息。将有一个用于从该页面下载 AWS Certified Cloud Practitioner 考试指南的链接。这些链接所提供的信息可帮助您准备认证考试。

请勿回复此电子邮件，如果您对 AWS 认证计划有任何问题，请联系我们，以获得帮助。

谢谢。 
```




-----

LVS-NAT based on EC2 amazon linux

-----

siyin training - finished

![]()
-----

내일 출발 

야딩 ticket

-----

20190630 1차 BD 

-----





-----

django + wxapp + aws beanstalk

-----

190603 지원했음 

-----

spring cloud

-----

lvs

springcloud

-----


1. fighting!!!!!!ㅋㅠ    
-----

1. hadoop MapReduce hive spark
-----

1. redis

-----

1. AWS Lamda + AWS batch + AWS BeansTalk
    - lambda spider
2. blog
    - s3 website + cloudfront + Certifacate manager + route53

-----

1. docker networking
2. interview prapare
    - 경력직 면접 
    - recuiter view
3. resume modify


