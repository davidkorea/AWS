# HA_Wordpress

1. Create VPC
    - Subnets
      - 2 public subnets
      - 2 private subnets
    - Security Group
      - WebDMZ: HTTP + SSH
      - Mysql: 3306 to ONLY WebDMZ
2. IAM
    - EC2_S3_FULLACCESS
3. RDS mysql
    - Production - MySQL
        - Use Multi-AZ Deployment and Provisioned IOPS Storage as defaults for high availability and fast, consistent performance.
    - db.t2.micro — 1 vCPU, 1 GiB RAM
    - Multi-AZ deployment, Create replica in different zone
    - Storage type: General Purpose(SSD)
    - wpdb, root, 11111111
    - Virtual Private Cloud (VPC): myVPC
    - Availability zone: No Preference
    - Choose existing VPC security groups: mysql_myVPC
    - wpdb
4. EFS
