
- Postgres and MySQL are both supported as Aurora DB
- Aurora storage automatically grows in increments of 10GB, up to 64 TB
- Failover in Aurora is instantaneous. It’s HA native
- **Single Aurora** can have **15 read replicas** while **MySQL has 5** read replicas
- For **Aurora Global** You can add up to **16 Aurora Replicas** to the secondary cluster, allowing you to scale reads beyond the capacity of a single Aurora cluster
# 1. Aurora High Availability and Read Scaling

<p align="center">
    <img src="https://i.loli.net/2019/08/08/t4yps2MCrq6Zzli.png"  width="300" height="250">
    <img src="https://i.loli.net/2019/08/08/VIHdKQoCRPTaney.png"  width="500" height="250">
</p>


- 6 copies of your data across 3 AZ:
  - 4 copies out of 6 needed for writes
  - 3 copies out of 6 need for reads
  - Self healing with peer-to-peer replication
  - Storage is striped across 100s of volumes
- **One writer and multiple readers**
    - Supports multiple reader instances connected to the same storage volume as a single writer instance. This is a good general-purpose option for most workloads.  
    - **One Aurora Instance takes writes (master, primary) in ONE Aurora Cluster**, the others are Aurora Read Replicas
    - Master + up to 15 Aurora Read Replicas serve reads
    
- Automated failover for master in less than 30 seconds, from read replicas through **failover priority**
  - If there is contention争吵 between 2 or more replicas in the **same priority tier**, then Amazon RDS will promote the replica that is the **same size as the primary/master/write instance**
  - Priority tier logic: Tier 0 > Tier 1 > … > Tier 15
  ![](https://i.loli.net/2019/08/08/wiyVdMlkCsxXv8S.png)
- Support for Cross Region Read Replication
  ![](https://i.loli.net/2019/08/08/IFlUBYy61RxWzrA.png)

- Database Location
    - **Regional**, You provision your Aurora database in a **single AWS Region**
    - **Global**, You can provision your Aurora database in **multiple AWS Regions**. **Writes in the primary AWS Region** are replicated with typical latency of less than 1 sec to secondary AWS Regions. 通常以不到 1 秒的延迟将主 AWS 区域中的写入, 复制到辅助 AWS 区域。
        - Global全局Aurora依然可以设置Multi-AZ
        - 但是好像无法指定使用哪些Region？？？

# 2. Aurora Global Database
An Aurora global database consists of **one primary AWS Region** where your data is mastered, and **one read-only, secondary AWS Region**
- The Aurora cluster in the **primary AWS Region** where your data is mastered performs both read and write operations**. 
- The cluster in the **secondary region** enables low-latency reads. 
    - You can scale up the secondary cluster independently by **adding one or more DB instances (Aurora Replicas)** to serve read-only workloads. You can **add up to 16 Aurora Replicas** to the secondary cluster, whereas **Single Aurora** can have **15 read replicas**
    - The secondary cluster enables **fast failover for disaster recovery**. You can typically **promote a secondary cluster** and make it **available for writes in under a minute**

- **Aurora Global Database** is only available for Aurora with **MySQL 5.6 compatibility**
- You **can't use db.t2 or db.t3** instance classes for an Aurora global database. You have a **choice of db.r4 or db.r5** instance classes.

# 3. Aurora Serverless
- No need to choose an instance size
- Only supports MySQL 5.6 (as of Jan 2019) & Postgres (beta)
    - MySQL 5.6-compatible, Aurora Serverless capacity is only available with this edition.
- Helpful when you can’t predict the workload
- DB cluster starts, shutdown and scales automatically based on CPU / connections
- **Can migrate from Aurora Cluster to Aurora Serverless and vice versa**
- Aurora Serverless usage is measured in ACU (Aurora Capacity Units)
- Billed in 5 minutes increment of ACU
- Note: **some features of Aurora aren’t supported in Serverless mode**, be sure to check the documentation if you plan on using it


![](https://i.loli.net/2019/08/21/ZxHO2zymAb8dCfN.png)
