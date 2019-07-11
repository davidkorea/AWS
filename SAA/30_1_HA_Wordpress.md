# HA_Wordpress

## 1. Archetcting
1. Create VPC
    - Subnets
      - 2 public subnets
      - 2 private subnets
    - Security Group
      - WebDMZ: **NFS** + HTTP + SSH
      - Mysql: 3306 to ONLY WebDMZ
2. IAM
    - EC2_S3_FULLACCESS
3. RDS mysql
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
5. EC2： Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type
    - myVPC
    - public subnet
    - IAM
## 2. Install Wordpress on EC2
1. install httpd php

- `yum update -y`
- `yum install httpd24 -y`
- `chkconfig httpd on`
- `yum -y install php55 php55-bcmath php55-devel php55-common php55-cli php55-pecl-apc php55-pdo php55-mysql php55-xml php55-gd php55-mbstring php-pear php55-mysqlnd php55-mcrypt`

2. mount EFS
- `sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-e6e16987.efs.ap-northeast-2.amazonaws.com:/ /var/www/html/`
```
[root@ip-10-0-11-167 ~]# df -h
Filesystem                                      Size  Used Avail Use% Mounted on
devtmpfs                                        483M   64K  483M   1% /dev
tmpfs                                           493M     0  493M   0% /dev/shm
/dev/xvda1                                      7.9G  1.3G  6.5G  16% /
fs-e6e16987.efs.ap-northeast-2.amazonaws.com:/  8.0E     0  8.0E   0% /var/www/html
```
![](https://i.loli.net/2019/07/11/5d26f34e469f148742.png)
3. create phpinfo.php
```

[root@ip-10-0-11-167 ~]# cd /var/www/html/
[root@ip-10-0-11-167 html]# echo "<?php phpinfo(); ?>" > phpinfo.php
[root@ip-10-0-11-167 html]# ls
phpinfo.php
```
4. start httpd
```
[root@ip-10-0-11-167 html]# service httpd start
Starting httpd:                                            [  OK  ]

[root@ip-10-0-11-167 html]# netstat -anutp | grep 80
tcp        0      0 :::80                       :::*      LISTEN      8514/httpd
udp        0      0 fe80::8:e2ff:fe6c:2eae:546  :::*                  2227/dhclient
```
- get access to 13.125.29.184 and 13.125.29.184/phpinfo.php success

5. download wordpress
- `wget https://wordpress.org/latest.zip`, download to /var/www/html 
- `unzip latest.zip`
- `mv /var/www/html/wordpress/* /var/www/html/`, let httpd recognize the wordpress codes `mv wordpress/* ./`
- `rm -rf wordpress/ latest.zip`, delete downloaded and unziped files
- `sudo chown -R apache:apache /var/www/html/*`, let apach own the /var/www/html/*
- `chmod -R 775 /var/www/html`, owner(apache user) rwx, group(apache) r-x, others r-x

get access to ec2-13-125-29-184.ap-northeast-2.compute.amazonaws.com with error **Your server is running PHP version 5.5.38 but WordPress 5.2.2 requires at least 5.6.20.**, get access to public ip with http error 500
```diff
/var/www/html/wp-includes/version.php

 - 37 #$required_php_version = '5.6.20';
 + 37 $required_php_version = '5.2.0';
```




































