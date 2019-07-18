# Route53_GeolocationRouting


Different from Latency based!
- This is routing based on user location
- Here we specify: traffic from the UK should go to this specific IP
- Should create a **“default”** policy (in case there’s no match on location)
  ![](https://i.loli.net/2019/07/18/5d3001171eaf853684.png)


![](https://i.loli.net/2019/07/09/5d2430a501a3a64747.png)


# 1. Health Checks
由于之前停掉了EC2，再次启动IP会变化。因此需要更新健康检查的IP地址
![](https://i.loli.net/2019/07/09/5d243189823b320990.png)

# 2. Geolocation Routing Policy
可以按照 大洲 和 国家 来选择。此处将USA的web服务器设置成Asia来访问。
![](https://i.loli.net/2019/07/09/5d2432b3a5b5757547.png)

# 3. Geoproximity Routing Policy (Traffic Flow Only)

Geo-proximity地理接近

![](https://i.loli.net/2019/07/09/5d2439861b79d46838.png)
![](https://i.loli.net/2019/07/09/5d243989323ff85269.png)

![](https://i.loli.net/2019/07/09/5d2438e8c9e7d15430.png)
