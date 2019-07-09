# Route53_SimpleRouting

- 一个A记录可以包含多可web server的ip地址
- 使用简单路由规则，会随机分配给A记录中的某一个ip

  ![](https://i.loli.net/2019/07/09/5d2405eaaab4789670.png)

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
