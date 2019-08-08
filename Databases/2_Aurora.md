
- Postgres and MySQL are both supported as Aurora DB
- Aurora storage automatically grows in increments of 10GB, up to 64 TB
- Failover in Aurora is instantaneous. It’s HA native
- Aurora can have 15 read replicas while MySQL has 5

# 1. Aurora High Availability and Read Scaling

![](https://i.loli.net/2019/08/08/t4yps2MCrq6Zzli.png)
![](https://i.loli.net/2019/08/08/VIHdKQoCRPTaney.png)

- 6 copies of your data across 3 AZ:
  - 4 copies out of 6 needed for writes
  - 3 copies out of 6 need for reads
  - Self healing with peer-to-peer replication
  - Storage is striped across 100s of volumes
- One Aurora Instance takes writes (master, primary)
- Master + up to 15 Aurora Read Replicas serve reads
- Automated failover for master in less than 30 seconds, from read replicas through failover priority
  - If there is contention between 2 or more replicas in the same priority tier, then Amazon RDS will promote the replica that is the same size as the primary instance
  - Priority tier logic: Tier 0 > Tier 1 > … > Tier 15
  ![](https://i.loli.net/2019/08/08/wiyVdMlkCsxXv8S.png)
- Support for Cross Region Replication
