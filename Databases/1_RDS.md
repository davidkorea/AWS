
# 1. RDS Read Replicas
- Up to 5 Read Replicas, 
- Within AZ, **Multi-AZ**, Cross AZ or Cross Region
- Replication is **ASYNC**, so reads are eventually consistent
- Replicas can be promoted to their own DB，只读副本可以升级为Master读写DB
- Applications must update the connection string to leverage read replicas，独立于Master之外的单独URL Endpoint
- 目前Read Replicas支持以下4个数据库：
  - Aurora
  - PostgreSQL
  - MySQL
  - MariaDB

# 2. RDS Multi AZ
- SYNC replication
- One DNS name – automatic app failover to standby
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention干预，干涉 in apps

# 3. RDS Backups
- Backups are automatically enabled in RDS
- Automated backups:
  - Daily full snapshot of the database
  - Capture transaction logs in real time => ability to restore to any point in time
  - 7 days retention (can be increased to 35 days)
- DB Snapshots:
  - Manually triggered by the user
  - Retention of backup for as long as you want
