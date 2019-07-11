# HA Word Press Site

1. S3
    - code bucket
    - media bucket
  
2. CloudFront
   - distribution with media bucket
  
3. RDS mysql

4. VPC Security Group
  - WebDMZ：HTTP
  - MYSQL: open to WebDMZ group
5. IAM
    - EC2 with S3fullaccess
6. EC2
    - IAM role
    - bootsttap code(user data)
    - security group
  
# 1. Architect
### 1.1 S3
leave all settings by default.

- wp-code-davidkorea
- wp-image-davidkorea

### 1.2. CloudFront
leave all settings by default.

- Origin Domain Name: wp-image-davidkorea.s3.amazonaws.com
### 1.3. 3.RDS mysql
- Dev/Test - MySQL
- **db.t2.small — 1 vCPU, 2 GiB RAM**
- Multi-AZ deployment
  - Creates a replica in a different Availability Zone (AZ) to provide data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups.
- Storage type: General Purpose（SSD）
- DB instance identifier
  - wpdb
  - root，11111111
- Public accessibility： No
- Create new VPC security group
- Database name：wpdb

### 1.4. VPC Security Group
- 将创建RDS时生成的安全组，添加一条规则。把WebDMZ加入到RDS安全组中
  ![](https://i.loli.net/2019/07/10/5d25b2c1be1c485517.png)
  ![](https://i.loli.net/2019/07/10/5d25b2c1cfffb33967.png)
  
### 1.5. IAM
EC2_S3_FULLACCESS

### 1.6. EC2
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
# 2. WP Settings
## 2.1 Create a Post
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
  
  [root@ip-172-31-28-229 html]# cd wp-content/uploads/2019/07/
  [root@ip-172-31-28-229 07]# ls
  image1.png
  ```
## 2.2 Backup image content -> s3
  
> So what we want to do is we want to make it so that every time somebody uploads a file to our WordPress site that that file is **also stored in S3 for redundancy** and eventuall what we're going to do is we're going to force **cloudfront to serve those files**, ather than using the images on our EC2 instance. Because then the site will load a bit faster.

- `aws s3 cp --recursive /var/www/html/wp-content/uploads/ s3://wp-image-davidkorea`
    ```
    [root@ip-172-31-28-229 html]# aws s3 cp --recursive /var/www/html/wp-content/uploads/ s3://wp-image-davidkorea

    upload: wp-content/uploads/2019/07/image1.png to s3://wp-image-davidkorea/2019/07/image1.png
    ```  
  
  
## 2.3 Backup full WP code -> s3
  
> The next thing we want to do is add a bit of redundancy. we could lose this EC2 instance at any time. So what we want is a full copy of our website in our S3 bucket. if we lose our EC2 instance we can have an auto scaling group. As soon as they boot they pull down the code from the S3 bucket.

- `aws s3 cp --recursive /var/www/html  s3://wp-code-davidkorea`
    ```
    [root@ip-172-31-28-229 html]# aws s3 cp --recursive /var/www/html  s3://wp-code-davidkorea
    ```
    ```
    [root@ip-172-31-28-229 html]# aws s3 ls s3://wp-code-davidkorea
                               PRE wp-admin/
                               PRE wp-content/
                               PRE wp-includes/
    2019-07-10 15:16:09        165 .htaccess
    2019-07-10 15:16:09          8 healthy.html
    2019-07-10 15:16:09        420 index.php
    2019-07-10 15:16:09      19935 license.txt
    2019-07-10 15:16:09       7425 readme.html
    2019-07-10 15:16:09       6919 wp-activate.php
    2019-07-10 15:16:12        369 wp-blog-header.php
    2019-07-10 15:16:12       2283 wp-comments-post.php
    2019-07-10 15:16:12       2898 wp-config-sample.php
    2019-07-10 15:16:12       3121 wp-config.php
    2019-07-10 15:16:13       3847 wp-cron.php
    2019-07-10 15:16:18       2502 wp-links-opml.php
    2019-07-10 15:16:18       3306 wp-load.php
    2019-07-10 15:16:18      38883 wp-login.php
    2019-07-10 15:16:18       8403 wp-mail.php
    2019-07-10 15:16:18      17947 wp-settings.php
    2019-07-10 15:16:18      31085 wp-signup.php
    2019-07-10 15:16:18       4764 wp-trackback.php
    2019-07-10 15:16:18       3068 xmlrpc.php
    ```
## 2.3 re-writer url -> cloudfront
> POST image url will change to cloudfront url in 10mins or 1-2hours

- change the url `^wp-content/uploads/(.*)$` to `http://d6b0b5wvk29ki.cloudfront.net/$1`

  ```
  [root@ip-172-31-28-229 html]# cat .htaccess
  Options +FollowSymlinks
  RewriteEngine on
  rewriterule ^wp-content/uploads/(.*)$ http://d6b0b5wvk29ki.cloudfront.net/$1 [r=301,nc]
  ```
- sync the modified file to s3
  ```
  [root@ip-172-31-28-229 html]# aws s3 sync /var/www/html s3://wp-code-davidkorea
  Completed 165 Bytes/~165 Bytes (3.9 KiB/s) with ~1 file(s) remaining (calculatinupload: ./.htaccess to s3://wp-code-davidkorea/.htaccess
  Completed 165 Bytes/~165 Bytes (3.9 KiB/s) with ~0 file(s) remaining (calculatin
  ```
- allow httpd to override
  - /etc/httpd/conf/httpd.conf
  ```diff
    146     #
    147     # AllowOverride controls what directives may be placed in .htaccess files.
    148     # It can be "All", "None", or any combination of the keywords:
    149     #   Options FileInfo AuthConfig Limit
    150     #
  - 151     AllowOverride None
  + 151     AllowOverride All
  ```
  ```
  [root@ip-172-31-28-229 conf]# systemctl restart httpd
  ```
## 2.4 Make image S3 public
1. 阻止公共访问权限[关闭]
2. policy
  ```json
   {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "PublicReadGetObject",
              "Effect": "Allow",
              "Principal": "*",
              "Action": "s3:GetObject",
              "Resource": "arn:aws:s3:::wp-image-davidkorea/*"
          }
      ]
  }
  ```
now，access http://13.125.224.163/wp-content/uploads/2019/07/image1.png  
  
  
  

  
  
  
  
  
  


