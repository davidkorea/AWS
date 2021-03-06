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
- Backup: 0days
    - A backup retention period of zero days will disable automated backups for this DB Instance.
- Disable all other options
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
- `vim wp-config.php` and copy code to this file under `/var/www/html`
    ```php
    <?php
    /**
     * The base configuration for WordPress
     *
     * The wp-config.php creation script uses this file during the
     * installation. You don't have to use the web site, you can
     * copy this file to "wp-config.php" and fill in the values.
     *
     * This file contains the following configurations:
     *
     * * MySQL settings
     * * Secret keys
     * * Database table prefix
     * * ABSPATH
     *
     * @link https://codex.wordpress.org/Editing_wp-config.php
     *
     * @package WordPress
     */

    // ** MySQL settings - You can get this info from your web host ** //
    /** The name of the database for WordPress */
    define( 'DB_NAME', 'wpdb' );

    /** MySQL database username */
    define( 'DB_USER', 'root' );

    /** MySQL database password */
    define( 'DB_PASSWORD', '11111111' );

    /** MySQL hostname */
    define( 'DB_HOST', 'wpdb.cjsmtj0wnlp6.ap-northeast-2.rds.amazonaws.com' );

    /** Database Charset to use in creating database tables. */
    define( 'DB_CHARSET', 'utf8mb4' );

    /** The Database Collate type. Don't change this if in doubt. */
    define( 'DB_COLLATE', '' );

    /**#@+
     * Authentication Unique Keys and Salts.
     *
     * Change these to different unique phrases!
     * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
     * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
     *
     * @since 2.6.0
     */
    define( 'AUTH_KEY',         'A|o6!U]o+o@>{d$Nj^g</A%|y1>]Yqn8ebT8aPiO7yUli?W)Ug!Jkc`[gG$P&t`9' );
    define( 'SECURE_AUTH_KEY',  'k#Hq=9n*$^u;Dj,=*[!>G5mr~!l[C269-iiJWs_Yi]{-C%bElyRGU0s(5wMZXR3C' );
    define( 'LOGGED_IN_KEY',    'v$%tA*r$P5v,u@3*gR[j6^Ec*WVNQk/|D,.A(>@A&q)5Kz$gI/5N[Gs?|Y>UuZ >' );
    define( 'NONCE_KEY',        ',2U.]qY4.]+G5z)C<5*K=K+nk>n`paxwfV~r Rb_hw4.S:dVxNxq>:tanp&242y@' );
    define( 'AUTH_SALT',        'GEy$r:Y#gO3em,zA{)E4WyPcGa>]N9w2mQw-}c<7z?MLl0T)cx<1(i!tsa<CY[0b' );
    define( 'SECURE_AUTH_SALT', '5ZQ-o~Hyi}y0s4P5o;(O.o1_8(k:G;T@qy#Nh(-}|GL931Q_nOmIl:(hfOf]4]ef' );
    define( 'LOGGED_IN_SALT',   'rS~iAjcTb-E?P,&mh+DWkg`$k{3kRtu5g|oYO<fj6lNsTK8F3{(HrfvE2(iL%Qzy' );
    define( 'NONCE_SALT',       ']Q|zp:7?:NEDanCVa6:pnJe8$w;#?lR~j^WR<vUT;nXAq}hU<%fwvf8/ U_eTqRG' );

    /**#@-*/

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each
     * a unique prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';

    /**
     * For developers: WordPress debugging mode.
     *
     * Change this to true to enable the display of notices during development.
     * It is strongly recommended that plugin and theme developers use WP_DEBUG
     * in their development environments.
     *
     * For information on other constants that can be used for debugging,
     * visit the Codex.
     *
     * @link https://codex.wordpress.org/Debugging_in_WordPress
     */
    define( 'WP_DEBUG', false );

    /* That's all, stop editing! Happy publishing. */

    /** Absolute path to the WordPress directory. */
    if ( ! defined( 'ABSPATH' ) ) {
        define( 'ABSPATH', dirname( __FILE__ ) . '/' );
    }

    /** Sets up WordPress vars and included files. */
    require_once( ABSPATH . 'wp-settings.php' );

    ```
    ![](https://i.loli.net/2019/07/11/5d2695a526fdf25785.png)    
    ![](https://i.loli.net/2019/07/11/5d2695a90f04c63764.png)
    ![](https://i.loli.net/2019/07/11/5d2695ab9060532275.png)
- Create a new Post
- Check post content
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
  - **若未开启S3 versioning，同名文件会被覆盖**
  
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
- `systemctl restart httpd`, 一定要重启httpd

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
  
# 3. HA
## 3.1 Load Balancer
- myWPALBA
- All AZs
- Select an existing security group： WebDMZ
- Target group
    - myWPTG
- Health checks
    - Path: /healthy.html
- Advanced health check settings
    - Healthy threshold: 2
    - Unhealthy threshold: 3
    - Timeout: 5 seconds
    - Interval: 6 seconds
- Register Targets: add exsited EC2

## 3.2 Route53
A - alias - elb
![](https://i.loli.net/2019/07/11/5d269e8db048c55503.png)
  
## 3.3 Write/Read De-couple
  
- writer node ---push---> S3, /etc/crontab
- reader node ---pull---> S3, /etc/crontab
- Route53 ---> ELB(reader nodes)

### 3.3.1 Create AMI for Reader Node

make the AMI on the exsiting EC2
- sync from S3 every 1min
    ```
    vim /etc/crontab

    */1 * * * * root aws s3 sync --delete s3://wp-code-davidkorea /var/www/html
    ```
    - `--delete` full copy with s3, if no --delete, only new item on S3 will be cp to EC2. but deleted items will not be delete on EC2


- `service crond restart`, 立即生效crontab
    - 本地创建一个test.txt文档，上传至S3，执行重启crond服务，查看/var/www/html目录可以同步到该文件
- Create AMI from EC2
    - 此时远程SSH连接的EC2会掉线
        ![](https://i.loli.net/2019/07/11/5d26a4248f81946774.png)
        
### 3.3.2 Single EC2 for Writer Node

- change crontab command for writer node
    ```
    */1 * * * * root aws s3 sync --delete /var/www/html  s3://wp-code-davidkorea
    */1 * * * * root aws s3 sync --delete /var/www/html/wp-content/uploads S3://wp-image-davidkorea
    ```
- go to /var/www/html create test1.txt and `service crond restart`

## 3.4 Auto Scaling Group
- My AMIs: myWPreadernode - ami-0e721768b15812854
- Create Launch Configuration
    - myWPLC
    - IAM role: EC2_S3_FULLACCESS
    - Advanced Details
        ```
        #!/bin/bash
        yum -y update
        aws s3 sync --delete s3://wp-code-davidkorea /var/www/html
        ```
- Security Group: WebDMZ
- Create Auto Scaling Group
    ![](https://i.loli.net/2019/07/11/5d26af6181bce17236.png)
    ![](https://i.loli.net/2019/07/11/5d26af64844b719307.png)    

- Keep this group at its initial size

- 将之前的EC2从LB中移除，将刚创建的ReaderNode ASG加入
    ![](https://i.loli.net/2019/07/11/5d26b18c05c0781145.png)
    ![](https://i.loli.net/2019/07/11/5d26b2ab4d4da34456.png)
    
- when create a new post, the image url will be redirect to cloudfront, and this may last 10mins
    ![](https://i.loli.net/2019/07/11/5d26c3e1e2ece28660.png)



## 3.5 RDS Failover
Failover will last 1-2mins down.

![](https://i.loli.net/2019/07/11/5d26c56aa531b67457.png)
![](https://i.loli.net/2019/07/11/5d26c56d1195952401.png)



# 4. Conclusion

**Bad Architecture**

- when restart writer node(the original EC2 we created), WP will down
- because the full WP code that the **Reader Nodes** copied that contains the ip belongs to the **writer node**
- when writer node down or restart, the ip will be changed and all reader node cannot serve the dynamic content(only raw static content with no css webpage) due to that its code contains the original writer node ip and network settings

![](https://i.loli.net/2019/07/11/5d26cbe494f9391472.png)



