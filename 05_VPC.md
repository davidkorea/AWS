
1. VPC
    - IPv4 CIDR block 10.0.0.0/16 
2. Subnet
    - Public Subnet ```subnet-02aecb6c4e9f890d0 | my-public-subnet```
      - 指定上面创建的VPC
      - 可用区域 ap-northeast-2a，2b不可用
      - IPv4 CIDR block 10.0.0.0/24
    - Private Subnet
      - 指定上面创建的VPC
      - 可用区域 ap-northeast-2a
      - IPv4 CIDR block 10.0.10.0/24
3. Internet Gateway ```igw-09ff3e23ea474f1a1```
    - 只需命名一个网关接口即可
4. NAT Gateway
    - 子网 public Subnet，NAT网关需要在public子网内创建
    - 分配公网弹性 IP
5. Route Table
    - Public Subnet
        - 创建只需指定名称和VPC
        - 创建后编辑路由，添加规则
        
            |Destination|Target|Status|Propagated|
            |-|-|-|-|
            |10.0.0.0/16	|local	|active|No	|
            |0.0.0.0/0|igw-09ff3e23ea474f1a1	|active|No|
        - 关联子网 ```subnet-02aecb6c4e9f890d0 | my-public-subnet```
