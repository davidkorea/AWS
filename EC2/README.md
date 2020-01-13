# EC2

![2355C07D-0219-4662-ABD3-72B406A3817A](https://user-images.githubusercontent.com/26485327/72266626-1bd72180-3662-11ea-8c0e-1f410a2fc9bc.png)





# Dedicated Host

![4A40A7CF-9EFF-4953-8A42-AFA2EF7D75CF](https://user-images.githubusercontent.com/26485327/72216385-9e2cec00-3563-11ea-806b-e4c86aab8ddf.png)


- Not all AMIs supported
- If not multiple type uspported dedicated host, can run same instance type & size as dedicated host when purchase
    - AWS Nitro hypervisor based instance family support both `one type` and `mulitple type` instance in same family launch on a dedicated host
    - xen based instance type support only `one type`
- can run multiple VPC on a dedicated host

![E757FE7A-E37F-4B64-9D93-49017DD47F41](https://user-images.githubusercontent.com/26485327/72216358-1fd04a00-3563-11ea-9f35-03207f1db316.jpeg)
![56C9D7A7-A789-4FE2-883F-A407E0F94C7B](https://user-images.githubusercontent.com/26485327/72216359-2068e080-3563-11ea-801a-2410b1ca9a57.jpeg)

- can purchase reservation

![6940F4BC-D9B6-430F-9207-C67B4A5ECFB9](https://user-images.githubusercontent.com/26485327/72216413-e3511e00-3563-11ea-9ed2-c3da56216df8.jpeg)





-----

### billing granularity 收费颗粒度
- linux instance - charge per second, 1h 5mins = charge 65 second
- windows & linux distributions - charge per hour, 1h 5mins = charge 2 hour

-----

## load balancer + auto scaling + spot & on-demand
prerequisites before create ASG
1. create Launch Template
    - Enable "auto assign public IP" if use non-default VPC
2. create ELB
    - for CLB, no need to create target group
    - for ALB, create blank target group

![D4261581-1988-4940-A1D2-D81E215452E4](https://user-images.githubusercontent.com/26485327/71803879-42ff8300-30a5-11ea-9bf1-6edc7901156c.jpeg)
![14935280-4F6F-4CB9-8C82-B1DC99296A43](https://user-images.githubusercontent.com/26485327/71803880-42ff8300-30a5-11ea-9f45-3da06bfb7be2.jpeg)
![image](https://user-images.githubusercontent.com/26485327/71803707-e3a17300-30a4-11ea-8820-ce88735f029d.png)
![A9061C6F-9107-4C19-A85C-A6711522C51B](https://user-images.githubusercontent.com/26485327/71804368-9b835000-30a6-11ea-88a1-f7eba03be6b8.jpeg)


![22452B58-5B0B-49B1-A692-6EF63476886D](https://user-images.githubusercontent.com/26485327/71804111-db960300-30a5-11ea-8ffc-d06ef28994b4.jpeg)
EC2 instances created by ASG will be registered with LB automatically.
- for Classic LB, no need target group, EC2 can be registered to CLB directly
- for ALB, registered into target group automatically

----

built on the Nitro system are the C5, C5d, M5, M5d, P3dn, A1, R5, R5a, and other latest-generation EC2 instances.


## Dedicated Host vs Dedicated Instance

![95683B0D-F9E6-441C-BEA5-CF98DB1031DE](https://user-images.githubusercontent.com/26485327/71713144-d50c4f00-2e4b-11ea-8484-076f114b9574.jpeg)

### 1. 专用主机 
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html

- 按照专用主机计费
- 唯一AWS account使用物理机，可以与同AWS organization的其他AWS Account分享同一个主机
- 根据专用主机配置，自行划分不同配置虚拟机
- 当一个专用主机出现故障后，会自动将该主机上的虚拟机复制到另外一台专用主机上面，下面2种除外
  - 已停止的EC2
  - Instance Store EC2
- BYOL 软件
- RHEL, SUSE, and Windows AMIs 不可以用作虚拟机操作系统，即专用主机上面不能创建RHEL SUSE Windows虚拟机
![AE0A8CC0-ED9D-4254-B6E3-AA0613807DD0](https://user-images.githubusercontent.com/26485327/72216147-9455b980-3560-11ea-8fd2-d7f45a138536.jpeg)

![B274F7F2-695A-4339-8567-DA9DC8E8050A](https://user-images.githubusercontent.com/26485327/72216073-8a7f8680-355f-11ea-96be-9a68fa2bc881.jpeg)

- X - instances that run on a Dedicated Host can only be launched in a VPC 


### 2. 专用实例
- 按照实例收费
- 唯一AWS account使用物理机
- 由虚拟化软件在物理主机上切分出虚拟机实例，即dedicated instance


### 3. Bare Metal主机
- 无虚拟化hypervisor, better performance than the same spec shared instance becuase there is no virtulization layer
- 一台完整的主机作为一个EC2 instance，不可再切分为小的虚拟机

## GPU
### 1. Elastic Graphics Accelerator

Seoul Region NOT supported，for Windows EC2 ONLY, connect to EC2 with network, install driver on EC2 before 2018.01

![8A98678A-0BDF-4DA6-BAD4-41FFC3645C7A](https://user-images.githubusercontent.com/26485327/71763014-4a465580-2f1a-11ea-9028-b3cca9e41b42.jpeg)


![A3DF41A3-0DC4-44F3-981C-62DDE7EB5D8B](https://user-images.githubusercontent.com/26485327/71763064-c476da00-2f1a-11ea-8e3f-3502e0721014.jpeg)


![72E0CF5C-057F-42E2-861E-8D82297DC52A](https://user-images.githubusercontent.com/26485327/71763134-64346800-2f1b-11ea-9c09-31525a1e8fe0.jpeg)



**Compare GPU instamce and EG instance**

- G3 instances 1, 2, 4, 8 GPU cards with 8G memory
- M, T, C, R, X, Z, H, I, and D type instances, when creating, add not a whole GPU card with 1, 2, 4, 8G memory
  
### 2. Elastic Inference

- P type instance is good for training machine learning models with full GPU power
- when use a already trained model to make predictions, do not need full GPU power to do the inference process
- 1 ~ 32 trillion floating point operations per second 万亿每秒浮点运算（TFLOPS），P3 instance start at 125 TFLOPS
- support TensorFlow and Apache MXNet
- all size ok, test t2.nano and t2.micro
- Not for Windows instance, find only Elastic Graphics in t2.medium and above
- test AWS linux, Ubuntu, and RHEL ok
- GPU connect to instance over network by VPC Endpoint
![47854826-082E-4E30-9657-9E63843B949B](https://user-images.githubusercontent.com/26485327/71766757-b76fe000-2f46-11ea-84a4-95b5bc351496.png)

![A8C79FDD-3E2A-4BA1-8384-D1E6266601ED](https://user-images.githubusercontent.com/26485327/71766784-39600900-2f47-11ea-9d78-15cc772315cb.jpeg)


![14610485-60A9-4620-9C66-74FDDD811619](https://user-images.githubusercontent.com/26485327/71712024-df781a00-2e46-11ea-8ffe-93ef8cff9301.jpeg)



## Elastic Compute Units (ECUs) - Intel CPU ONLY

**1 ECU is basically 1Ghz of Intel Xeon processing power**

compare computing power with different CPU(**Intel powered CPU ONLY**)

instances from the C5 type are powered by Intel Skylake processors, while M4 instances are powered by Intel Haswell processors.
- c5.xlaege - 4 vCPUs, 17 ECUs
- m4.xlarge - 4 vCPUs, 13 ECUs


## Naming

![62AEE143-256B-4FD2-A5EE-A6CFCFAAFB60](https://user-images.githubusercontent.com/26485327/71705426-002e7880-2e23-11ea-9abd-9d9e6ef94979.jpeg)


## Families & types

https://aws.amazon.com/cn/ec2/instance-types/

![14676BBE-8F57-497F-B996-4822220B9787](https://user-images.githubusercontent.com/26485327/71702883-3664fb80-2e15-11ea-82f8-e8d3e13f9545.jpeg)
![7746288A-C164-4ED3-ACEB-B3BD6510B072](https://user-images.githubusercontent.com/26485327/71702884-3664fb80-2e15-11ea-9e82-3b93079cfe67.jpeg)
![FDF4ABC6-79C4-4887-9D01-75A735778351](https://user-images.githubusercontent.com/26485327/71702885-3664fb80-2e15-11ea-8cf8-9d9f5ba3bf54.jpeg)
![6B04DBB6-E6DC-4DC3-AD90-7198969F7A50](https://user-images.githubusercontent.com/26485327/71702886-36fd9200-2e15-11ea-94e9-fb77955b5d07.jpeg)
![A041F2A8-3D3D-4195-A625-93776A56B02D](https://user-images.githubusercontent.com/26485327/71702887-36fd9200-2e15-11ea-9989-6653fc8b76f7.jpeg)
- I - IO
- D - dense storage
- H - HDD














































