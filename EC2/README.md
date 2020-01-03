# EC2

built on the Nitro system are the C5, C5d, M5, M5d, P3dn, A1, R5, R5a, and other latest-generation EC2 instances.


## Dedicated Host vs Dedicated Instance

![95683B0D-F9E6-441C-BEA5-CF98DB1031DE](https://user-images.githubusercontent.com/26485327/71713144-d50c4f00-2e4b-11ea-8484-076f114b9574.jpeg)

### 1. 专用主机 Bare Metal
- 唯一AWS account使用物理机
- 无虚拟化
- 有些license要求无虚拟化


### 2. 专用实例

- 唯一AWS account使用物理机
- 由虚拟化软件切分出虚拟机实例，即dedicated instance
- BYOL 软件




## Elastic Graphics Acceleratoe

![14610485-60A9-4620-9C66-74FDDD811619](https://user-images.githubusercontent.com/26485327/71712024-df781a00-2e46-11ea-8ffe-93ef8cff9301.jpeg)

- G3 instances 
  - 1 GPU card with 8G memory
  - 2
  - 4 
  - 8 

- M, T, C, R, X, Z, H, I, and D type instances, when creating, add not a whole GPU card with
  - 1G memory
  - 2
  - 4
  - 8
  




## Elastic Compute Units (ECUs)

compare computing power with different CPU.

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














































