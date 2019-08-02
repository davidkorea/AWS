# Route53_Simple_Multivalue_Routing

- 一个A记录可以包含多可web server的ip地址
- 使用简单路由规则，会随机分配给A记录中的某一个ip
  - If multiple values are returned, a random one is chosen by the client(broswer)
- **Sinple Routing Policy**
  - You can’t attach health checks to simple routing policy
  
  ![](https://i.loli.net/2019/07/09/5d2405eaaab4789670.png)
- **Multivalue Routing Policy**
  - 每次只能绑定一个IP，有多少个IP，则创建多少个Multivalue策略的记录
  - Your company hosts 10 web servers all serving the same web content in AWS. They want Route 53 to serve traffic to random web servers. Which routing policy will meet this requirement, and provide the best resiliency? 因为有健康检查
  ![](https://i.loli.net/2019/07/09/5d243abe9ab3871263.png)

## 1. create 2 EC2 in different Region

- 54.180.90.132
  ```
  #!/bin/bash
  yum update -y
  yum install httpd -y
  chkconfig httpd on
  service httpd start
  cd /var/www/html
  echo "<html><h1>This is the web server from Seoul</h1></html>" > index.html
  ```
- 3.86.9.74
  ```
  #!/bin/bash
  yum update -y
  yum install httpd -y
  chkconfig httpd on
  service httpd start
  cd /var/www/html
  echo "<html><h1>This is the web server from USA</h1></html>" > index.html
  ```

## 2. Sinple Routing Policy
![](https://i.loli.net/2019/07/09/5d240c3ba261440137.png)

## 3. Multivalue Routing Policy

- **Sinple + Health Check = Multivalue**

![](https://i.loli.net/2019/07/09/5d243bbf7f4c375842.png)
