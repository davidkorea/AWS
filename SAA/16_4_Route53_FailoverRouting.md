# Route53_FailoverRouting
-  configure active-passive failover using the failover routing policy. 
- Use an active-passive failover configuration when you want a primary resource or group of resources to be availablethe majority of the time and you want a secondary resource or group of resources to be on standby in case all the primary resources become unavailable. When responding to queries, Route 53 includes only the healthy primary resources.If all the primary resources are unhealthy, Route 53 begins to include only the healthy secondary resources in response to DNS queries.

## 1. Failover Routing policy
- 可以设置**多个primary** 和 **多个secondary**

![](https://i.loli.net/2019/07/09/5d242e3e421f690469.png)
![](https://i.loli.net/2019/07/09/5d2430534a50667570.png)

## 2. 关闭Primary web server
需等待5-10mins，DNS会自动Failover
![](https://i.loli.net/2019/07/09/5d242fd057a8752445.png)
