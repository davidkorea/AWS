# AWS

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


