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
