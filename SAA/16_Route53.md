# Route53总结


> **You have deployed a web application targeting a global audience across multiple AWS Regions under the domain name.example.com. You decide to use Route53 Latency-Based Routing to serve web requests to users from the region closest to the user. To provide business continuity in the event of server downtime you configure weighted record sets associated with two web servers in separate Availability Zones per region. Dunning a DR test you notice that when you disable all web servers in one of the regions Route53 does not automatically direct all users to the other region. What could be happening? {Choose 2 answers)**

- a. Latency resource record sets cannot be used in combination with weighted resource record sets.
- b. You did not setup an HTTP health check to one or more of the weighted resource record sets associated with the disabled web servers.
- c. The value of the weight associated with the latency alias resource record set in the region with the disabled servers is higher than the weight for the other region.
- d. One of the two working web servers in the other region did not pass its HTTP health check.
- e. You did not set "Evaluate Target Health" to "Yes" on the latency alias resource record set associated with example com in the region where you disabled the servers.

解析： How Health Checks Work in Complex Amazon Route 53 Configurations Checking the health of resources in complex configurations works much the same way as in simple configurations. However, in complex configurations, you use a **combination of alias resource recordsets (including weighted alias, latency alias, and failover alias)** and nonalias resource record sets to build a decision tree that gives you greater control over how Amazon Route 53 responds to requests. For more information, see How Health Checks Work in Simple Amazon Route 53 Configurations.

Answer: [b], [e]
- 同时使用了Latency-Based Routing，weighted Routing
- 除了Simple Routing，其他方式都有健康检查，而Failover的健康检查必须设定
-----

Choosing a Routing Policy: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-latency

Active-Active and Active-Passive Failover: https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-types.html

# 10. In AWS, the most common records
- **A: URL to IPv4**
- **AAAA: URL to IPv6**
- **CNAME: URL to URL**,
  - An Amazon Route 53 CNAME record can point to any DNS record hosted anywhere.
  - app.mydomain.com => abc.anything.com
- **Alias: URL to AWS resource**
  - Alias可以连接到AWS的资源，以及当前HostZone中的记录
  - app.mydomain.com => abc.elb.**amazonaws.com**
  - An Alias record can map one DNS name to another Amazon Route 53 DNS name
  - Free of charge
  - Native health check
    ![](https://i.loli.net/2019/07/18/5d2fe9192afe941584.png)
  - TTL can **NOT** be set for an Alias record in Amazon Route 53.
    ![](https://i.loli.net/2019/07/18/5d3011936e48412016.png)


# 20. Active-Active and Active-Passive Failover
You can use Route 53 health checking to configure active-active and active-passive failover configurations. 
## active-active
- You configure **active-active** failover using any routing policy (or combination of routing policies) **other than failover routing policy**
  - 主动-主动模式，就是使用除了failover routing policy路由策**之外**的其他一个或多个策略
- Use this failover configuration when you want **all of your resources to be availablethe** majority of the time. When a resourcebecomes unavailable, Route 53 can detect that it's unhealthy and stop includingit when responding to queries. 
  - 所有web server都有机会被分配到流量来处理，如果健康检查失败，那么不再将流量发送到该实例
## active-passive
- and you configure **active-passive** failover using the **failover routing policy**. 
  - 主动-被动模式，就是只是用failover routing policy路由策略

- Use an active-passive failover configuration when you want a primary resource or groupof resources to be availablethe majority of the time and you want a secondary resource or group of resourcesto be on standby in case all theprimary resources become unavailable. When responding to queries, Route 53 includesonly the healthy primary resources.If all the primary resources are unhealthy, Route 53 begins to include only thehealthy secondary resources in response to DNS queries. 
  - 如果主动的资源中，还有健康的，那么所有流量发送到主动中的健康EC2实例，因为可以设置多个primary，和多个secondary
  - 当所有主动的实例都挂掉了，才会启动被动中的健康实例
















-----

-----

- With Route 53, there is a default limit of 50 domain names. However, this limit can be increased by contacting AWS support.
- Your company hosts 10 web servers all serving the same web content in AWS. They want Route 53 to serve traffic to random web servers. Which routing policy will meet this requirement, and provide the best resiliency?
  - simple
  - weighted
  - **multivalue**, random 选择，需要高可靠性。因为multivalue可以健康检查，而simple不可以
  - latency









- 弹性负载均衡器（ELB）没有固定的IPv4地址，在使用ELB的时候永远使用它的DNS名字。很多场景下我们需要绑定DNS记录到ELB的endpoint地址，而不绑定任何IP
- 需要熟记Alias记录和CNAME的区别，也可以参考一下在别名和非别名记录之间做出选择
- 考试中，如果出现选择Alias记录和CNAME记录的选择，95%的情况都要选择Alias记录

在AWS Route53中有多种不同的路由策略（Routing Policy），我们可以根据自己的不同需求将我们的DNS解析到不同的目标上去。

- 简单路由策略（Simple Routing Policy）：提供单一资源的策略类型，即一个DNS域名指向一个单一目标
- 加权路由策略（Weighted Rouing Policy）：按照不同的权值比例将流量分配到不同的目标上去
- 延迟路由策略（Latency Routing Policy）：根据网络延迟的不同，将与用户延迟最小的结果应答给最终用户
- 地理位置路由策略（Geolocation Routing Policy）：根据用户所在的地理位置，将不同的目标结果应答给用户
- 故障转移路由策略（Failover Routing Policy）：配置主动/被动（Active/Passive）的故障转移策略，保证DNS解析的容灾

AWS Route53是所有AWS考试的基础，因此熟知这个服务的内容对于使用AWS服务以及通过AWS认证都是非常重要的。


![](https://i.loli.net/2019/06/25/5d11caee6d5bb64927.png)
![](https://i.loli.net/2019/06/25/5d11caf1208c454663.png)
![](https://i.loli.net/2019/06/25/5d11caf3f173253442.png)
![](https://i.loli.net/2019/06/25/5d11caf62dabf88172.png)
![](https://i.loli.net/2019/06/25/5d11caf94602639641.png)
![](https://i.loli.net/2019/06/25/5d11cafc194e163718.png)
![](https://i.loli.net/2019/06/25/5d11cafe8635f10590.png)

![](https://i.loli.net/2019/07/09/5d2404548d73369798.png)
