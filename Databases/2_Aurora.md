
- Postgres and MySQL are both supported as Aurora DB
- Aurora storage automatically grows in increments of 10GB, up to 64 TB
- Failover in Aurora is instantaneous. It’s HA native
- Aurora can have 15 read replicas while MySQL has 5

# 1. Aurora High Availability and Read Scaling
- 6 copies of your data across 3 AZ:
  - 4 copies out of 6 needed for writes
  - 3 copies out of 6 need for reads
  - Self healing with peer-to-peer replication
  - Storage is striped across 100s of volumes
- One Aurora Instance takes writes (master)
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas serve reads
- Support for Cross Region Replication
