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
    - Security Groups: webDMZ_myvpc
