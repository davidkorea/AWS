- **“全部”意味着，只有同属于这个安全组的EC2，全部类型流量/协议/端口可以相互通信，“全部”并不代表可以访问互联网**
  - **EFS设置时，因为选择的default没有任何规则，所以额外打开EFS Port**
  ![](https://i.loli.net/2019/06/16/5d063954dde0389700.png)
  
- **ACL - subnet子网级别**，外部流量先到达ACL，如果ACL拒绝，即使安全组放行，也依然会被拒绝
  - 一个Subnet只能关联一个ACL，一个ACL可以被绑定至多个Subnet
  - 创建VPC时，同时被创建的默认ACL，默认会放行全部流量。自己创建的ACL默认会拒绝所有流量
- **安全组 - instance实例级别**

- **NAT网关可以跨AZ，~~因为本身公有子网和私有子网就是在不同的AZ~~，所以NAT GW放在一个AZ的公有子网，将另一个AZ的私有子网流量转发至NAT GW**
  ![](https://i.loli.net/2019/08/02/5d43f2375340d84635.png)
  
- **一个VPC可以创建多个NAT网关，但只能创建一个Internet网关**
- **NAT网关创建于一个AZ中的一个共有子网中。如果该AZ挂掉，则所有通过此NAT网关的私有子网都将失去Internet连接。处于高可用考虑，每个AZ设置一个共有子网，并在其内设置NAT网关**
- **NAT网关不支持DNAT，但NAT实例可配置DNAT**
- **终端节点创建后，公网实例会优先通过终端节点访问S3吗？？？？？？？？？？**
- **虽然10.0.0.0 - 10.255.255.255 （10/8）网段可用，但是只能是`10.255/16` 才被允许创建，`10/8不被允许 `**
- **每个人账号中看到的ap-northeast-2b，并不带表实际上的同一个物理IDC机房，AZ名称是随机的，不绑定特定机房**

# 1. VPC简介

Amazon Virtual Private Cloud (Amazon VPC)允许你在已定义的虚拟网络内启动AWS资源。这个虚拟网络与你在数据中心中运行的传统网络极其相似，并会为你提供使用AWS的可扩展基础设施的优势。

简单来说，VPC就是一个AWS用来隔离你的网络与其他客户网络的虚拟网络服务。在一个VPC里面，用户的数据会逻辑上地与其他AWS租户分离，用以保障数据安全。

可以简单地理解为一个VPC就是一个虚拟的数据中心，在这个虚拟数据中心内我们可以创建不同的子网（公有网络和私有网络），搭建我们的网页服务器，应用服务器，数据库服务器等等服务。

VPC有如下特点：
- VPC内可以创建多个子网，可以在选择的子网上启动EC2实例
- 在每一个子网上分配自己规划的IP地址
- 每一个子网配置自己的路由表
- 创建一个Internet Gateway并且绑定到VPC上，让EC2实例可以访问互联网
- VPC对你的AWS资源有更安全的保护
  - 部署针对实例的安全组（Security Group）
  - 部署针对子网的网络控制列表（Network Access Control List）
- 一个VPC可以跨越多个可用区（AZ）
- **一个Subnet只能在一个可用区（AZ）内**，1个AZ可创建多个Subnet
- 安全组（Security Group）是有状态的，而网络控制列表（Network Access Control List）是无状态的
  - 有状态：如果入向流量被允许，则出向的响应流量会被自动允许
  - 无状态：入向规则和出向规则需要分别单独配置，互不影响
- VPC的子网掩码范围是从/28到/16，不能设置在这个范围外的子网掩码
- VPC可以通过Virtual Private Gateway (VGW) 来与企业本地的数据中心相连
- VPC可以通过AWS PrivateLink访问其他AWS账户托管的服务（VPC终端节点服务）

![](https://i.loli.net/2019/06/17/5d06fd0a9c76080795.png)
![](https://i.loli.net/2019/06/17/5d07042e2275c59512.png)

## 1.1 默认VPC
- 在每一个区域（Region），AWS都有一个默认的VPC
- 在这个VPC里面所有子网都绑定了一个路由表，其中有默认路由（目的地址 0.0.0.0/0）到互联网
- 所有在默认VPC内启动的EC2实例都可以直接访问互联网
- 在默认VPC内启动的EC2实例都会被分配公网地址和私有地址
如下图所示，我们在某一个区域内有一个VPC，这个VPC的网络是172.31.0.0/16

在这个VPC内有2个子网，分别是172.31.0.0/20 和 172.31.16.0/20。这两个子网内都有一个EC2实例，每一个实例拥有一个该子网的私有地址（172.31.x.x）以及一个AWS分配的公网IP地址（203.0.113.x）。

这两个实例关联了一个主路由表，该路由表拥有一个访问172.31.0.0/16 VPC内流量的路由条目；还有一个目的为 0.0.0.0/0 的默认路由条目，指向Internet网关。

因此这两个实例都可以通过Internet网关访问外网。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/06/default-vpc-diagram.png)

## 1.2 RFC1918私有地址范围
IETF RFC1918定义了私有网络的地址范围，这些私有网络一般仅用于企业和集团内部，并且这些地址在因特网上是不能路由的。

在我们进行VPC的网络设置以及子网的设置时，都必须使用这些私有网络地址。

私有地址里面的IPv4地址如下：

- 10.0.0.0/8，地址范围是10.0.0.0 – 10.255.255.255
- 172.16.0.0/12，地址范围是172.16.0.0 – 172.31.255.255
- 192.168.0.0/16，地址范围是192.168.0.0 – 192.168.255.255

但是因为VPC的子网掩码范围是还会限制只能是从/28到/16，因此你不能设置一个网络是172.16.0.0/12的VPC或者是192.168.0.0/29的VPC。

- 10.0.0.0：网络地址。
- 10.0.0.1：由 AWS 保留，用于 VPC 路由器。
- 10.0.0.2：由 AWS 保留。DNS 服务器的 IP 地址始终为 VPC 网络范围的基址 + 2；但是，我们也保留了每个子网范围基址 + 2 的 IP 地址。对于包含多个 CIDR 块的 VPC，DNS 服务器的 IP 地址位于主要 CIDR 中。有关更多信息，请参阅Amazon DNS 服务器。
- 10.0.0.3：由 AWS 保留，供将来使用。
- 10.0.0.255：网络广播地址。我们在 VPC 中不支持广播，因此我们会保留此地址。



## 1.3 EC2-Classic
在2013年12月4日之前，AWS提供了一种叫做EC2-Classic的网络，可以算是EC2-VPC的前身。

在EC2-Classic内，你所运行的EC2实例会和其他客户的EC2实例共享同一个扁平的网络，而不是隔离的虚拟网络。

在2013年12月4日之后创建的AWS账户，账户中只会有VPC。

这一部分大概了解即可，如果需要更详细的信息，可以查看EC2-Classic与 EC2-VPC 的区别。

## 1.4 VPC Peering
VPC Peering可是两个VPC之间的网络连接，通过此连接，你可以使用IPv4地址在两个VPC之间传输流量。这两个VPC内的实例会和如果在同一个网络一样彼此通信。

- 可以通过AWS内网将一个VPC与另一个VPC相连
- 同一个AWS账号内的2个VPC可以进行VPC Peering
- 不同AWS账号内的VPC也可以进行VPC Peering
- 不支持VPC Transitive Peering
  - 如果VPC A和VPC B做了Peering
  - 而且VPC B和VPC C做了Peering
  - 那么VPC A是不能和VPC C进行通信的
  - 要通信，只能将VPC A和VPC C进行Peering
如下图，VPC A和VPC B进行了Peering之后，子网10.0.0.0/16和172.31.0.0/16会被打通，并且可以无阻地互相访问。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/06/peering-intro-diagram.png)

## 1.5 弹性 IP （Elastic IP）
弹性IP是专门用来分配AWS服务的IPv4地址，通过申请弹性IP地址，你可以将一个固定的公网IP分配给一个EC2实例。在这个实例无论重启，关闭，甚至终止之后，你都可以回收这个弹性IP地址并且在需要的时候分配给一个新的EC2实例。

默认情况下，AWS分配的公网IP地址都是浮动的，这意味着如果你关闭再启动你的EC2实例，这个地址也会被释放并且重新分配。但是弹性IP地址是和你的AWS账号绑定的，除非你手动释放掉这个地址，否则这个地址可以一直被你拥有。

值得注意的是，弹性IP地址在绑定了running状态的EC2实例才是免费的；但是如果已经申请了的弹性IP地址没有关联任何运行的EC2实例，则AWS会对这个空闲的弹性IP收费，这是为了避免资源的浪费。

## 1.6 NAT Instance & NAT Gateway
### NAT实例（NAT Instance）
- 创建NAT实例之后，一定要关闭源/目标检查（Source/Destination Check）
- NAT实例需要创建在公有子网内
- 私有子网需要创建一条默认路由（0.0.0.0/0），指到NAT实例
- NAT实例的瓶颈在于实例的大小，如果遇到了网络吞吐瓶颈，你可以加大实例类型
- 需要自己创建弹性伸缩组（Auto Scaling Group），自定义脚本来达到NAT实例的高可用（比如部署在多个可用区）
- 需要关联一个安全组（Security Group）
  ![](https://i.loli.net/2019/06/17/5d070ad0f1d4455776.png)
  
### NAT网关（NAT Gateway）
- 网络吞吐可以达到10Gbps
- 不需要为NAT的操作系统和程序打补丁
- 不需要关联安全组
- 自动分配一个公网IP地址（EIP）
- 私有子网需要创建一条默认路由（0.0.0.0/0）到NAT网关
- 不需要更改源/目标检查（Source/Destination Check）
- **处于高可用考虑，每个AZ创建一个公有子网，每个公有子网中创建一个NAT网关。以免某个AZ挂掉，所有私有子网无法联网**
  - if you have resources in multiple Availability Zones and they share one NAT gateway in the event that the Nat gateways availability zone is down then your resources in the availability zones and the other Availability Zones are going to lose Internet access.
  - create a NAT gateway in each availability zone and configure your routing to ensure that resources use the NAT gateway in the same availability zone that they're in. 
  
## 1.7 VPC Flow Log
- 对于Peer VPC不能开启Flow Logs功能，除非这个VPC也在你的账户内
- 不能给Flow Logs打标签
- Flow Logs创建后不能更改其配置

VPC Flow Logs并不捕获所有经过VPC的流量，以下流量将不会被捕获：
- 实例访问Amazon DNS服务器（即.2地址）的流量
- Windows进行Windows许可证激活的流量
- 访问实例Metadata的流量（即去往169.254.169.254的流量）
- DHCP流量
- 访问VPC路由器的流量（即.1地址）
![](https://i.loli.net/2019/06/17/5d0730c16903148242.png)




# 2. 创建VPC
## 2.1 创建在VPC中的EC2没有公有DNS
  - VPC - Edit DNS hostnames
  
![](https://i.loli.net/2019/06/16/5d0649ffd50c489776.png)
## 2.2 创建在VPC中的EC2没有自动分配公网IP
  - 子网 - 修改自动分配 IP 设置
  
![](https://i.loli.net/2019/06/16/5d064a067a00481964.png)
## 2.3 Internet Gateway

> - **一个VPC只能创建一个Internet网关，将所有公有子网绑定至该IGW**

- 创建internet网关igw
- 创建一张路由表my_route_igw，**不使用创建子网时自动创建的默认的路由表**
- 规则

  |Destination|Target|Status|Propagated|
  |-|-|-|-|
  |10.0.0.0/16	|local	|active|No	|
  |0.0.0.0/0|igw-09ff3e23ea474f1a1	|active|No|

- 显式关联关联VPC中的名为public的2个子网，使得两个子网分可以直接访问Internet

![](https://i.loli.net/2019/06/16/5d0650c879db580131.png)

## 2.4 NAT Gateway
> - **NAT网关可以跨AZ，因为本身公有子网和私有子网就是在不同的AZ，所以NAT GW放在一个AZ的公有子网，将另一个AZ的私有子网流量转发至NAT GW**
> - **一个VPC可以创建多个NAT网关**，同一个AZ中的公有，私有子网进行NAT转发，而不用跨AZ进行NAT转发

- 在public subnet中，创建NAT网关
- 创建一张路由表my_route_nat，**不使用创建子网时自动创建的默认的路由表**
- 规则

  |Destination|Target|Status|Propagated|
  |-|-|-|-|
  |10.0.0.0/16	|local	|active|No	|
  |0.0.0.0/0|nat-01a15ab52b03d1511	|active|No|
- 显式关联关联VPC中private subnet

![](https://i.loli.net/2019/06/16/5d0650cdb004622721.png)

- NAT网关收费 `$0.059 per NAT Gateway Hour`
  ![](https://i.loli.net/2019/06/17/5d06f6711c57f80686.png)

# 3. 终端节点
VPC终端节点能建立VPC和一些AWS服务之间的高速、私密的“专线”。这个专线叫做PrivateLink，使用了这个技术，你无需再使用Internet网关、NAT网关、VPN或AWS Direct Connect连接就可以访问到一些AWS资源！

终端节点（Endpoints）是虚拟设备，它是以能够自动水平扩展、高度冗余、高度可用的VPC组件设计而成，你也不需要为它的带宽限制和故障而有任何担忧。

AWS PrivateLink是专为客户设计用于特定用途的AWS内网，它采用了高度可用并且可扩展的架构（意味着你无需再为PrivateLink的性能和高可用性做任何额外架构设计）。

VPC终端有两种类型：接口和网关
- 网关类型支持以下服务（需要记住）：
  - Amazon S3
  - DynamoDB

- 接口类型支持以下服务（了解即可）：
  - Amazon CloudWatch Logs
  - AWS CodeBuild
  - Amazon EC2 API
  - Elastic Load Balancing API
  - AWS Key Management Service
  - Amazon Kinesis Data Streams
  - AWS Service Catalog
  - Amazon SNS
  - AWS Systems Manager
  - 其他 AWS 账户托管的终端节点服务
  - 支持的 AWS Marketplace 合作伙伴服务


## 3.1 创建终端节点
使得VPC中的实例不用通过IGW或者NAT访问到公网，再访问S3的公网url，直接使用AWS的私有地址访问自己的S3

- 为S3服务创建终端节点 
- 关联私有子网的路由表
- 私有子网的路由表中会自动添加一条到该终端节点的路由规则

## 3.2 测试终端节点
- 给私有子网的EC2实例赋予S3FullAccess IAM Role
- 取消私有子网关联NAT网关，确保私有子网的实例无法公网访问
- 通过公有子网的EC2，SSH到私有子网的EC2实例
- 执行`aws s3 ls`，若成功，则表示S3终端节点有效




# 4. 创建终端节点服务
- 您可以使用 PrivateLink 技术， 使您的VPC中的服务 可用于 其他AWS账户和服务
- PrivateLink 是一种高可用性的可扩展技术，用来实现对服务的私有访问
- 其他账户和服务可以创建接口终端节点来访问您的终端节点服务。了解更多[VPC 终端节点服务 (AWS PrivateLink)](https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/endpoint-service.html)

AWS PrivateLink 是一项具有高可用性的可扩展技术，使您能够将您的VPC私密地连接到支持的**AWS 服务**，**其他AWS账户托管的服务(VPC终端节点服务)** ，以及支持的 **AWS Marketplace 合作伙伴服务**。您无需 Internet 网关、NAT 设备、公有 IP 地址、AWS Direct Connect 连接或 VPN 连接来与服务通信。
您的 VPC 和 服务之间的流量不会脱离 Amazon 网络。

# 5. VPC对等连接（VPC Peering）
VPC对等连接（VPC Peering）是两个VPC之间的连接，通过VPC Peering，你可以使用私有地址让两个VPC之间相互通信，就像它们在同一个VPC内一样。

你可以在自己的两个VPC之间建立对等连接，也可以在自己的VPC与其他AWS账号的VPC之间建立对等连接，还可以在位于不同区域的VPC之间建立对等连接（在2017年11月之前VPC Peering还不支持跨区域的连接呢）。

如下图所示，VPC A和VPC B之间建立了对等连接，那么VPC A中网段10.0.0.0/16内的实例就可以和VPC B中网段172.31.0.0/16内的实例进行互相通信，仿佛它们是处于同一个内网一样。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/peering-intro-diagram.png)

VPC Peering不是传统意义上的网关或者VPN连接，它并不依赖于任何物理的设备，它也不会出现单点故障或者网络瓶颈。

知识点
- 如果两个VPC出现了地址覆盖/重复，那么这两个VPC不能做Peering
  - 例如10.0.0.0/16的VPC与10.0.0.0/24的VPC是不能做对等连接的
- 参与VPC Peering的两个VPC可以来自不同的区域（以前不支持）
- VPC Peering不支持Transitive Peering
  - 即如果VPC A与VPC B进行了对等连接
  - VPC B与VPC C进行了对等连接
  - VPC A是不能与VPC C进行直接通信的，必须再建立VPC A和VPC C的对等连接才可以


  
# 6. 网络ACL（NACL）
网络访问控制列表（NACL）与安全组（Security Group）类似，它能在子网的层面控制所有入站和出站的流量，为VPC提供更加安全的保障。
- **安全组 - instance实例级别**
- **ACL - subnet子网级别**
知识点
- 在你的默认VPC内会有一个默认的网络ACL（NACL），它会允许所有入向和出向的流量
  ![](https://i.loli.net/2019/06/16/5d0643b107e0a26085.png)
  
- 你可以创建一个自定义的网络ACL，在创建之初所有的入向和出向的流量都会被拒绝，除非进行手动更改
- 对于所有VPC内的子网，每一个子网都需要关联一个网络ACL。如果没有关联任何网络ACL，那么子网会关联默认的网络ACL
- 一个网络ACL可以关联多个子网，但一个子网只能关联一个网络ACL
- 网络ACL包含了一系列（允许或拒绝）的规则，网络ACL会按顺序执行，一旦匹配就结束，不会再继续往下匹配
  - **一旦匹配到前面的放行rule，将不会再执行后面的拒绝Rule，需要将DENY规则放在前面**
- 网络ACL有入向和出向的规则，每一条规则都可以配置允许或者拒绝
- 网络ACL是无状态的（安全组是有状态的）
  - 被允许的入向流量的响应流量必须被精准的出向规则所允许（反之亦然）
  - 一般至少需要允许临时端口（TCP 1024-65535）
  - 关于临时端口的知识，参考 [网络 ACL - 临时端口](https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports)
  ![](https://i.loli.net/2019/06/16/5d064693d18a377958.png)
  ![](https://i.loli.net/2019/06/17/5d0722abe10bb84619.png)
  
  - 以网站访问为例
    - client： 随机端口5000（1024～65535） 访问服务器80端口
    - server： 80端口 响应 客户端的端口5000
  
  
![](https://i.loli.net/2019/06/17/5d0726ec77d9f57024.png)
![](https://i.loli.net/2019/06/17/5d0726f0e7a2e24532.png)
  
# 7. Direct Connect线路
AWS Direct Connect线路可以让你通过以太网光纤线路连接你的内部网络与AWS Direct Connect Location，可以打通你的内部网络与AWS的网络，从而拥有高速率、低延迟，安全、可靠的专线网络。

一般来说，我们要搭建一条Direct Connect线路，需要先通过本地的网络服务提供商将我们内部网络接到一个同城市的Direct Connect Location （这个Location可以是Equinix, CoreSite, Digital Reality的数据中心，全球有几十个这样的地理位置）上。然后需要向AWS申请Cross Connect，将服务提供商的路由器直接连接到同一个机房不同机柜的AWS设备上。

通过这样的连接，我们可以端到端地利用专线的稳定性和高吞吐量访问我们位于AWS内的所有资源。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/direct_connect_overview.png)
![](https://i.loli.net/2019/06/17/5d073463b3b6133652.png)
## Direct Connect的特点
- AWS提供的Direct Connect的带宽是1Gbps或者是10Gbps
- 少于1Gbps速率的Direct Connect线路可以向AWS Direct Connect合作伙伴申请，可以申请50Mbps到500Mbps的线路
- Direct Connect的数据包使用802.1Q协议进行封装（Q-in-Q tagging）
## VPN连接和Direct Connect的区别
- VPN连接可以在数分钟之内就搭建成功。如果有紧急的业务需求，可以使用VPN连接。VPN连接是基于互联网线路的，因此带宽不高，稳定性也不好，但价格便宜
- AWS Direct Connect使用的是专线，你的数据不会跑在互联网上，是私有的、安全的网络
