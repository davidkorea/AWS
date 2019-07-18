# Route53总结


## In AWS, the most common records are:
- **A: URL to IPv4**
- **AAAA: URL to IPv6**
- **CNAME: URL to URL**, An Amazon Route 53 CNAME record can point to any DNS record hosted anywhere.
- **Alias: URL to AWS resource**, An Alias record can map one DNS name to another Amazon Route 53 DNS name.
  - Alias可以连接到AWS的资源，以及当前HostZone中的记录
    ![](https://i.loli.net/2019/07/18/5d2fe9192afe941584.png)






















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
