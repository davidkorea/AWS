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
