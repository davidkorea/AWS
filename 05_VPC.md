
1. VPC
    - IPv4 CIDR block 10.0.0.0/16 
2. Subnet
    - Public Subnet
      - 指定上面创建的VPC
      - 可用区域 ap-northeast-2a，2b不可用
      - IPv4 CIDR block 10.0.0.0/24
    - Private Subnet
      - 指定上面创建的VPC
      - 可用区域 ap-northeast-2a
      - IPv4 CIDR block 10.0.10.0/24
3. Internet Gateway Internet 网关
    - 只需命名一个网关接口即可
4. NAT Gateway
    - 子网 public Subnet，NAT网关需要在public子网内创建
    - 分配公网弹性 IP
5. 
