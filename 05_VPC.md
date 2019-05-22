
1. VPC
    - IPv4 CIDR block 10.0.0.0/16 
2. Subnet
    - Public Subnet ```subnet-02aecb6c4e9f890d0 | my-public-subnet```
      - 指定上面创建的VPC
      - 可用区域 ap-northeast-2a，2b不可用
      - IPv4 CIDR block 10.0.0.0/24
    - Private Subnet ```subnet-0c97f8ef6342297eb | my-internal-subnet```
      - 指定上面创建的VPC
      - 可用区域 ap-northeast-2a
      - IPv4 CIDR block 10.0.10.0/24
3. Internet Gateway ```igw-09ff3e23ea474f1a1```
    - 只需命名一个网关接口即可
4. NAT Gateway ```nat-01a15ab52b03d1511```
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
            - 所有VPC内部10.0.0.0/16 包含所有subnet可以通信
            - 此subnet内的所有流量可以通过公网网关访问internet
        - 关联子网 ```subnet-02aecb6c4e9f890d0 | my-public-subnet```
    - Private Subnet
        - 创建只需指定名称和VPC
        - 创建后编辑路由，添加规则
        
            |Destination|Target|Status|Propagated|
            |-|-|-|-|
            |10.0.0.0/16	|local	|active|No	|
            |0.0.0.0/0|nat-01a15ab52b03d1511	|active|No|
            - 所有VPC内部10.0.0.0/16 包含所有subnet可以通信
            - 此subnet内的所有流量可以通过NAT网关，将outboung流量转到public subnet，再通过igw转发至internet。但无法回访
        - 关联子网 ```subnet-0c97f8ef6342297eb | my-internal-subnet```
6. Security Group
    - 绑定VPC
    - Inbound Rules

        |类型|协议|端口范围|来源|
        |-|-|-|-|
        |SSH|TCP|22|0.0.0.0/0|
        |所有 ICMP - IPv4|全部|不适用|0.0.0.0/0|
    - Outbound Rules
    
    
    
    
    
