# HA_Wordpress

1. Create VPC
    - Subnets
      - 2 public subnets
      - 2 private subnets
    - Security Group
      - WebDMZ: HTTP + SSH
      - Mysql: 3306 to ONLY WebDMZ
