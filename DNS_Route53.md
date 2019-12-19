
# Route53

## 1. Domain
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
2. Hosted Zone由多条记录集Record set构成，也可以称作Zone file
3. 一个Record set（Zone file）代表一种类型的记录，常见类型有
    - A：ipv4
    - AAAA： ipv6
    - CNAME：
    - NS： Name Server，名称服务器，绑定一个域名和对应IP地址
    - SOA： Start of authority，定义一个托管区域（域名）的权威元信息authoritative meta information
    - ...


## 2. Traffic Flow
- 可视化界面创建复杂路由策略

![IMG_0052](https://user-images.githubusercontent.com/26485327/71156175-cbe38580-2282-11ea-95d3-4420c6b51703.jpeg)

- 只有traffic flow可以使用Geoproximity，根据经纬度设置


![IMG_0053](https://user-images.githubusercontent.com/26485327/71156562-94290d80-2283-11ea-9fac-8034d2461695.jpeg)


