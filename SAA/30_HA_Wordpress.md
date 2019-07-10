# HA Word Press Site

1.S3
  - code bucket
  - media bucket
  
2.CloudFront
  - distribution with media bucket
  
3.RDS mysql

4.VPC
  - Security Group
    - WebDMZ
    - MYSQL: open to WebDMZ group
5.IAM
  - EC2 with S3fullaccess
6.EC2
  - IAM role
  - bootsttap code(user data)
  - security group
  
# 1. S3
leave all settings by default.

- wp-code-davidkorea
- wp-image-davidkorea

# 2. CloudFront
leave all settings by default.

- Origin Domain Name: wp-image-davidkorea.s3.amazonaws.com
# 3. 3.RDS mysql
- Dev/Test - MySQL
- Multi-AZ deployment
  - Creates a replica in a different Availability Zone (AZ) to provide data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups.
- Storage type: General Purpose（SSD）
- DB instance identifier
  - wpdb
  - root，11111111
- Public accessibility： No
- Create new VPC security group
- Database name：wpdb

# 4. VPC Security Group
- 将创建RDS时生成的安全组，添加一条规则。把WebDMZ加入到RDS安全组中
  ![](https://i.loli.net/2019/07/10/5d25b2c1be1c485517.png)
  ![](https://i.loli.net/2019/07/10/5d25b2c1cfffb33967.png)
  
# 5. IAM
EC2_S3_FULLACCESS

# 6. EC2
- IAM
- boosstrap
  ```shell
  #!/bin/bash
  yum update -y
  yum install httpd php php-mysql -y
  cd /var/www/html
  echo "healthy" > healthy.html
  wget https://wordpress.org/wordpress-5.1.1.tar.gz
  tar -xzf wordpress-5.1.1.tar.gz
  cp -r wordpress/* /var/www/html/
  rm -rf wordpress
  rm -rf wordpress-5.1.1.tar.gz
  chmod -R 755 wp-content
  chown -R apache:apache wp-content
  wget https://s3.amazonaws.com/bucketforwordpresslab-donotdelete/htaccess.txt
  mv htaccess.txt .htaccess
  chkconfig httpd on
  service httpd start
  ```
- ssh, start httpd
  ```
  [root@ip-172-31-28-229 ~]# cd /var/www/html/
  [root@ip-172-31-28-229 html]# ls
  healthy.html     wp-admin              wp-cron.php        wp-mail.php
  index.php        wp-blog-header.php    wp-includes        wp-settings.php
  license.txt      wp-comments-post.php  wp-links-opml.php  wp-signup.php
  readme.html      wp-config-sample.php  wp-load.php        wp-trackback.php
  wp-activate.php  wp-content            wp-login.php       xmlrpc.php
  [root@ip-172-31-28-229 html]# cat .htaccess
  Options +FollowSymlinks
  RewriteEngine on
  rewriterule ^wp-content/uploads/(.*)$ http://d2jmrva8tfzxcq.cloudfront.net/$1 [r=301,nc]

  # BEGIN WordPress

  # END WordPress[root@ip-172-31-28-229 html]#
  
  [root@ip-172-31-28-229 html]# systemctl start httpd
  [root@ip-172-31-28-229 html]# systemctl status httpd
  ```
- 13.125.224.163 set wordpress

- post content
  ```
  [root@ip-172-31-28-229 html]# ls
  healthy.html     wp-blog-header.php    wp-includes        wp-signup.php
  index.php        wp-comments-post.php  wp-links-opml.php  wp-trackback.php
  license.txt      wp-config-sample.php  wp-load.php        xmlrpc.php
  readme.html      wp-config.php         wp-login.php
  wp-activate.php  wp-content            wp-mail.php
  wp-admin         wp-cron.php           wp-settings.php
  [root@ip-172-31-28-229 html]# cd wp-content/
  [root@ip-172-31-28-229 wp-content]# ls
  index.php  plugins  themes  uploads
  [root@ip-172-31-28-229 wp-content]# cd uploads/
  [root@ip-172-31-28-229 uploads]# ls
  2019
  [root@ip-172-31-28-229 uploads]# cd 2019/
  [root@ip-172-31-28-229 2019]# ls
  07
  [root@ip-172-31-28-229 2019]# cd 07/
  [root@ip-172-31-28-229 07]# ls
  image1.png
  ```
  
