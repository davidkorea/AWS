
# DNS

- 每个主机有本地的DNS解析记录，如linux `/etc/hosts`文件
  ```
  192.168.0.22 fileserver fileserver.com
  192.168.0.200 printserver printserver.com
  ```
- 如果本地不能解析，则将请求发送到网卡配置的DNS服务器，如`8.8.8.8`
## 1. Route53 Domain
### 1.1 Register a domain or transfer a domain from other registrar
- Register a domain on Route53
- Transfer from other registrar
  - unlocking the domain transfer setting in the registrar’s admin interface 
  - requesting an authorization code.
  - You’ll supply that code to Route 53 when you’re ready to do the transfer
  
- eave your domain with its current registrar, you can still use Route 53 to manage your DNS configuration
  - Simply copy the name server addresses included in your Route 53 record set 
  - paste them as the new name server values in your registrar’s admin interface
  
### 1.2 托管一个域名 Hosted Zone托管区域
1. 一个Hosted Zone就代表着托管了一个域名
2. Hosted Zone由多条记录集Record set构成，也可以称作Zone file，用于对该域名的属性配置。一个Record set（Zone file）代表一个类型的属性配置，常见类型有
    - A：ipv4，可以是所有公网可达IP，私有IP不可以，访问域名就等于访问该ip
    - AAAA： ipv6 同上
    - CNAME：可用于除`example.com`之外的下级域名，如`www.example.com`CNAME至`www.thisismyexanplesite.com`
    - NS： Name Server，名称服务器，绑定一个域名和对应IP地址。Route53为每个域名提供4个name server
    - SOA： Start of authority，定义一个托管区域（域名）的权威元信息authoritative meta information
    - ...
    - Alias：仅用于连接AWS上的服务，可用于主域名`example.com`,如`example.com`Alias至`elb.awsamazon.98769786978.net`

- 购买一个域名后，会自动生成一个Hosted Zone，比如`example.com.`, 是`.com`下的子域名。该托管区域下面同时会自动生成SOA和NS两条记录集
- 对于该域名的其他策略需要灵性设置，如latency，failover等路由策略
- 对于嵌套的复杂路由策略需要使用Route53 Traffic Flow

## 2. Route53 Traffic Flow
- 可视化界面创建复杂路由策略

![IMG_0052](https://user-images.githubusercontent.com/26485327/71156175-cbe38580-2282-11ea-95d3-4420c6b51703.jpeg)

- 只有traffic flow可以使用Geoproximity，根据经纬度设置


![IMG_0053](https://user-images.githubusercontent.com/26485327/71156562-94290d80-2283-11ea-9fac-8034d2461695.jpeg)


