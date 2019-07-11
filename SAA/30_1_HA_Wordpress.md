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
