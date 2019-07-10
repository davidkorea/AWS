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
  
  
  
  
  
  
