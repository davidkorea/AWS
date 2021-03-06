# HA_Wordpress

# 1. Archetcting
1. Create VPC
    - Subnets
      - 2 public subnets
      - 2 private subnets
    - Security Group
      - WebDMZ: **NFS** + HTTP + SSH
      - Mysql: 3306 to ONLY WebDMZ
2. IAM
    - EC2_S3_FULLACCESS
3. RDS mysql **(Free Tier)**
    > if cannot create multi-AZ due to no 2+ AZ subnets in the VPC, but your VPC did have 2+ subnet in different AZ. delete you VPC and re-create it. also re-create Security Group

    - Dev/Test - MySQL
    - db.t2.micro — 1 vCPU, 1 GiB RAM
    - Multi-AZ deployment, Create replica in different zone
    - Storage type: General Purpose(SSD)
    - wpdb, root, 11111111
    - Virtual Private Cloud (VPC): myVPC
    - Availability zone: No Preference
    - Choose existing VPC security groups: mysql_myVPC
    - wpdb
4. EFS
    - VPC: my_vpc
    - Security Groups: webDMZ_myvpc（NFS allowed for 10.0.0.0/16）
5. EC2： Amazon Linux AMI 2
    - myVPC
    - public subnet
    - IAM
# 2. Install Wordpress on EC2
## 2.1. install httpd php

- `yum update -y`
- ~~`yum install httpd24 -y`~~
- `yum install httpd -y`
- `chkconfig httpd on`
- ~~`yum -y install php55 php55-bcmath php55-devel php55-common php55-cli php55-pecl-apc php55-pdo php55-mysql php55-xml php55-gd php55-mbstring php-pear php55-mysqlnd php55-mcrypt`~~
- install php5.6.20
    - `rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm`
    - `rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm`   
    - `yum install -y php56w php56w-opcache php56w-xml php56w-mcrypt php56w-gd php56w-devel php56w-mysql php56w-intl php56w-mbstring`


## 2.2. mount EFS
- `sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-e6e16987.efs.ap-northeast-2.amazonaws.com:/ /var/www/html/`
- 挂载之后，需要重新再进入一下/var/www/html/ 目录，否则写入很慢，且写入不成功
    ```
    [root@ip-10-0-11-167 ~]# df -h
    Filesystem                                      Size  Used Avail Use% Mounted on
    devtmpfs                                        483M   64K  483M   1% /dev
    tmpfs                                           493M     0  493M   0% /dev/shm
    /dev/xvda1                                      7.9G  1.3G  6.5G  16% /
    fs-e6e16987.efs.ap-northeast-2.amazonaws.com:/  8.0E     0  8.0E   0% /var/www/html
    ```
    ![](https://i.loli.net/2019/07/11/5d26f34e469f148742.png)
- 开机自动挂载
    - `echo "fs-6f4ac20e.efs.ap-northeast-2.amazonaws.com:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,_netdev,noresvport 0 0" >> /etc/fstab`

## 2.3. create phpinfo.php
```
[root@ip-10-0-11-167 ~]# cd /var/www/html/
[root@ip-10-0-11-167 html]# echo "<?php phpinfo(); ?>" > phpinfo.php
[root@ip-10-0-11-167 html]# ls
phpinfo.php
```
## 2.4. start httpd
```
[root@ip-10-0-11-167 html]# service httpd start
Starting httpd:                                            [  OK  ]

[root@ip-10-0-11-167 html]# netstat -anutp | grep 80
tcp        0      0 :::80                       :::*      LISTEN      8514/httpd
udp        0      0 fe80::8:e2ff:fe6c:2eae:546  :::*                  2227/dhclient
```
get access to 13.125.29.184 and 13.125.29.184/phpinfo.php success

## 2.5. download wordpress
- `wget https://wordpress.org/latest.zip`, download to /var/www/html 
- `unzip latest.zip`
- `mv /var/www/html/wordpress/* /var/www/html/`, let httpd recognize the wordpress codes `mv wordpress/* ./`
- `rm -rf wordpress/ latest.zip`, delete downloaded and unziped files
- `sudo chown -R apache:apache /var/www/html/*`, let apach own the /var/www/html/*
- `chmod -R 775 /var/www/html`, owner(apache user) rwx, group(apache) r-x, others r-x

> get access to ec2-13-125-29-184.ap-northeast-2.compute.amazonaws.com with error **Your server is running PHP version 5.5.38 but WordPress 5.2.2 requires at least 5.6.20.**, get access to public ip with http error 500

            ```diff
            /var/www/html/wp-includes/version.php

             - 37 #$required_php_version = '5.6.20';
             + 37 $required_php_version = '5.2.0';
            ```

> ### 升级php版本 [Linux 升级PHP版本](https://blog.csdn.net/weixin_34824012/article/details/81056157)
- 查看当前ph版本
    ```
    [root@ip-10-0-11-28 html]# php -v
    PHP 5.4.16 (cli) (built: Sep  1 2018 05:47:37)
    Copyright (c) 1997-2013 The PHP Group
    Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
    ```
- 执行下面的命令升级软件仓库
    - `rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm`
    - `rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm`

        ```
        [root@ip-10-0-11-28 html]# rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
        Retrieving https://mirror.webtatic.com/yum/el7/epel-release.rpm
        warning: /var/tmp/rpm-tmp.t9DlBK: Header V4 RSA/SHA1 Signature, key ID 62e74ca5: NOKEY
        Preparing...                          ################################# [100%]
        Updating / installing...
           1:epel-release-7-5                 ################################# [100%]

        [root@ip-10-0-11-28 html]# rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
        Retrieving https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
        warning: /var/tmp/rpm-tmp.biDiiI: Header V4 RSA/SHA1 Signature, key ID 62e74ca5: NOKEY
        Preparing...                          ################################# [100%]
        Updating / installing...
           1:webtatic-release-7-3             ################################# [100%]
        ```
- 删除php
    - `yum remove php-common`

- 安装php 5.6版本（php56w-devel这个不是必需的）
    - `yum install -y php56w php56w-opcache php56w-xml php56w-mcrypt php56w-gd php56w-devel php56w-mysql php56w-intl php56w-mbstring`

- 查看版本
    ```    
    [root@ip-10-0-11-28 html]# php -v
    PHP 5.6.40 (cli) (built: Jan 12 2019 13:11:15)
    Copyright (c) 1997-2016 The PHP Group
    Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
        with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
    ```


# 3. 使用CloudFront进行内容分发服务
- 创建S3存储桶
- 创建CloudFront，源为S3存储桶
- 安装Wordpress创建WP Offload S3 Lite
- WordPress多媒体文件自动上传到S3
- URL重写，将本地多媒体文件的URL重写为S3的URL
- URL重写，将本地多媒体文件的URL重写为Cloudfront的URL
## 3.1 Create CloudFront Distribution

![](https://i.loli.net/2019/07/12/5d28023452ce282181.png)

- 源域名：S3
- 源路径：S3中的某个文件夹目录
- 限制存储桶访问：用户只能通过cloudfront的url来访问S3的内容，而非S3 url
- 源访问身份：创建新的IAM

## 3.2 Add Plugin to WP - WP Offload Media Lite
- 安装
    ![](https://i.loli.net/2019/07/12/5d2802faafe5776959.png)
    ![](https://i.loli.net/2019/07/12/5d2802fe2fde581131.png)
- 更改IAM访问，选择S3存储桶
    ![](https://i.loli.net/2019/07/12/5d280300ea0cb13997.png)
    ![](https://i.loli.net/2019/07/12/5d280343b719e86439.png)
    ![](https://i.loli.net/2019/07/12/5d2803472488e51305.png)

## 3.3 URL重写，将本地多媒体文件的URL重写为S3的URL

> 网站服务器挂掉了，上传的post内容依然存在S3中

- setting
    ![](https://i.loli.net/2019/07/12/5d2803548d81939446.png)
    ![](https://i.loli.net/2019/07/12/5d2803d053fb630906.png)
    ![](https://i.loli.net/2019/07/12/5d2803decf76626311.png)
    ![](https://i.loli.net/2019/07/12/5d2803e560d6f63893.png)
- 上传的图片，剪裁多尺寸保存
    ![](https://i.loli.net/2019/07/12/5d2803f219b5022044.png)

## 3.4 URL重写，将本地多媒体文件的URL重写为Cloudfront的URL

> 各地用户可以快速访问静态媒体文件

- setting
    ![](https://i.loli.net/2019/07/12/5d28044a1819c58297.png)
    ![](https://i.loli.net/2019/07/12/5d281135cddd589729.png)
    
    
# 4. 使用AutoScaling，Elastic Load Balancer和Route 53
- 创建Application Load Balancer
- 创建Route 53并使用Alias记录解析ELB
- 创建AMI镜像
- 创建启动配置（Launch Configuration）
- 创建Auto Scaling组

## 4.1 ALB
> 可以根据网站url的不同路径，来分发流量，给到ALB后端不同的目标组 ？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？

- select public subnets
    ![](https://i.loli.net/2019/07/12/5d2828cd41ef539966.png)
    ![](https://i.loli.net/2019/07/12/5d2828d6afa1913545.png)
- Target group and healthy check all by default
    ![](https://i.loli.net/2019/07/12/5d2828da28f6729201.png)

## 4.2 Route53

- A - alias - ALB
    ![](https://i.loli.net/2019/07/12/5d28296b7208c81100.png)
- change all url in wordpress site, or else only index page is our domain, but other urls are still EC2 url
    ![](https://i.loli.net/2019/07/12/5d28298a9479789942.png)
    ![](https://i.loli.net/2019/07/12/5d28298d2fc6496980.png)
    ![](https://i.loli.net/2019/07/12/5d28298fc6f0264698.png)


## 4.3 AutoScaling
### 4.3.1 Create WP AMI
- **一定要在源EC2中配置好，开机自动挂载EFS**
- EC2 instamce -> make AMI
- 创建AMI时，EC2会临时挂掉。SSH掉线，网站502错误。持续1min左右
    ![](https://i.loli.net/2019/07/12/5d282b0016fe351451.png)
### 4.3.2 Lanuch Config
- use the AMI create above 
    ![](https://i.loli.net/2019/07/12/5d282ed23c86086733.png)
    ![](https://i.loli.net/2019/07/12/5d282ed59aadb71746.png)
### 4.3.3 Auto Scaling Group
- my VPC, public subnets
- 高级详细信息：**将自动伸缩组中目标组里面的EC2实例，关联至负载均衡ALB**
    - 负载平衡，允许关联至ALB
    - 目标组，**将此处ASG中的实例，注册到ALB的目标组中，使得ASG中的实例来接收负载均衡的流量**
    ![](https://i.loli.net/2019/07/12/5d282ed8429a743218.png)
        - **错误：！！！！！健康检查ELB，EC2不可以！！！！！！！**

- **将此处ASG中的实例，注册到ALB的目标组中，使得ASG中的实例来接收负载均衡的流量**
    ![](https://i.loli.net/2019/07/12/5d2831795cc3890207.png)
    ![](https://i.loli.net/2019/07/12/5d28317c048bd31568.png)

#### 4.3.4 Test
- Test new EC2 that created by ASG ok
    ![](https://i.loli.net/2019/07/12/5d282edb293cc86226.png)

- shutdown the origin EC2, get access to domain name ok


# 5. Failover
## 5.1 EC2
- 删除ASg中的一个实例，网站访问正常，有一定延迟。因为ASG中的健康检查需要一定的时间

## 5.2 RDS 
- 强制重启mysql，选择failover选项。数据库会中断一定时间，网站也会随之中断
- 可以启用 Read Replica，虽然主（写入）数据库挂掉，但只读数据库正常，网站不会中断


