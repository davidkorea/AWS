
# 1. RDS Read Replicas
- Up to 5 Read Replicas
  - You can have Read Replicas of Read Replicas
- Within AZ, **Multi-AZ**, Cross AZ or **Cross Region**
- Replication is **ASYNC**, so reads are **eventually consistent**
- Replicas can be promoted to their own DB，只读副本可以升级为Master读写DB
- Applications must update the connection string to leverage read replicas，独立于Master之外的单独URL Endpoint
- Read Replicas **help with Disaster Recovery(DR)** by using a **Cross Region RR**
  ![](https://i.loli.net/2019/08/21/PRLd5uerVZNxbyj.png)
  
- Read Replicas can be used to run **BI / Analytics Reports** for example
- 目前Read Replicas支持以下4个数据库, NO Oracle! NO MS SQL
  - Aurora
  - PostgreSQL
  - MySQL
  - MariaDB
    ![](https://i.loli.net/2019/08/21/w2j1uvVGCA537Kt.png)
    
# 2. RDS Multi AZ (DR) in Single Region

- **Multi AZ** is only within a **single region**, **NOT cross region**. Region outages impact availability
- **SYNC** replication
- One DNS name – automatic app failover to standby
- The failover happens only in the following conditions:
  - The primary DB instance fails
  - An Availability Zone outage
  - The **DB instance** server **type** is **changed**
  - The **operating system** of the DB instance is undergoing **software patching**
  - A manual failover of the DB instance was initiated using Reboot with failover
  
- No failover for DB operations: long-running queries, deadlocks or database corruption errors.
- **Backups** and **snapshot** are **created from the standby**, will not stop the master db

# 3. RDS Backups
- Backups are automatically enabled in RDS
- Automated backups:
  - Backups happen during **maintenance windows**, **changes to the backup(maintenance) window take effect immediately** 
  - Daily full snapshot of the database
  - Backups are “continuous” and allow **point in time recovery**, Capture transaction logs in real time
  - 7 days retention (can be 0 to 35 days)
  - When you delete a DB instance, you can retain automated backups, 如要伸出数据库时，可以选择保留backup
- DB Snapshots:
  - Manually triggered by the user
  - Retention of backup for as long as you want, manual Snapshots don’t expire
  - Snapshots **takes IO operations** and can **stop database** from **seconds to minutes**
    - if **Multi-AZ**，**snapshot** created by **standby instance**, don’t impact the master – just the standby
  - Snapshots are incremental after the first snapshot (which is full)
  - You can take a “final snapshot” when you delete your DB
  
# 4. RDS Encryption
-  Encryption at rest:
  - Is done only when you **first create the DB** instance
  - Unencrypted DB => snapshot => copy snapshot as encrypted => create DB from snapshot
- Encryption at rest capability with AWS **KMS - AES-256** encryption
- SSL certificates to encrypt data to RDS in flight. To enforce SSL:
  - PostgreSQL: **`rds.force_ssl=1`** in the AWS RDS Console (Parameter Groups)
  - MySQL/MariaDB: Within the DB: **`GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL;`**
- To connect using SSL:
  - Provide the SSL Trust certificate (can be download from AWS)
  - Provide SSL options when connecting to database

# 5. RDS Security Groups
- RDS databases are usually deployed within a **private subnet**, not in a public one
- RDS Security works by leveraging security groups (the same concept as for EC2 instances) – it controls who can communicate with RDS
- **IAM policies** help control **who can manage AWS RDS**
- **Traditional Username and Password** can be used to **login to the database**
  - **IAM users** can now be used too (for **MySQL / Aurora** – NEW!)










# 5. DB Parameter Groups
- You can configure the DB engine using Parameter Groups
- **Dynamic parameters** are **applied immediately**
- **Static parameters** are **applied after instance reboot**
- You can modify parameter group associated with a DB (must reboot)

# 6. RDS API for SysOps
- `DescribeDBInstances` API –
  - Helps to get a list of all the DB Instances you have deployed including **Read Replicas**
  - Helps to get the **DB version**
- `CreateDBSnapshot` API – Make a snapshot of a DB
- `DescribeEvents` API - Helps to return information about **events related** to your DB Instance
- `RebootDBInstance` API - Helps to initiate a ‘forced’ failover by rebooting DB instance


# 7. RDS with CloudWatch
- CloudWatch metrics associated with RDS (gathered from the hypervisor):
  - Database Connections
  - SwapUsage
  - ReadIOPS / WriteIOPS
  - ReadLatency / WriteLatency
  - ReadThroughPut / WriteThroughPut
  - DiskQueueDepth
  - FreeStorageSpace
- Enhanced Monitoring (gathered from an agent on the DB instance)
  - Useful when you need to see how different **processes or threads use the CPU**
  - Access to over 50 new CPU, memory, file system, and disk I/O metrics


# 8. RDS Performance Insights
- Visualize your database performance and analyze any issues that affect it
- With the Performance Insights dashboard, you can visualize the database load and filter the load:
  - By Waits => find the resource that is the bottleneck (CPU, IO, lock, etc…)
  - By SQL statements => find the SQL statement that is the problem
  - By Hosts => find the server that is using the most our DB
  - By Users => find the user that is using the most our DB
- DBLoad = the number of active sessions for the DB engine
- You can view the SQL queries that are putting load on your database































