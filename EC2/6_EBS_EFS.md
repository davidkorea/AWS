- If you use EBS for high performance, use **EBS-optimized instance types**
- If an EBS volume is unused, you still pay for it. For cost saving over a long period, it can be cheaper to snapshot a volume and restore it later if unused

- High wait time or slow response for SSD => increase IOPS
- EC2 won’t start with EBS volume as root: make sure volume names are properly mapped (**/dev/xvdb** instead of **/dev/xvda** for example)
- After increasing a volume size, you still need to **repartition** to use the incremental storage (xfs_growfs for example)
- EBS is **already redundant** storage (replicated within an AZ)
- But what if you want to increase IOPS to say 100 000 IOPS?
  - RAID0
    - But one disk fails, all the data is failed
    - An application that needs a lot of IOPS and doesn’t need fault-tolerance
    - A database that has replication already built-in，已创建副本的数据库
   - RAID 1
    - Mirroring a volume to another
    - Application that need increase volume fault tolerance
    - Application where you need to service disks，提供存储服务的应用，云硬盘等
- EBS volumes are locked at the AZ level
- EFS mounting 100s of instances


# 1. EBS Volume
It’s a **network drive** (i.e. not a physical drive)
- It uses the network to communicate the instance, which means there might be a bit of latency
- It can be detached from an EC2 instance and attached to another one quickly

It’s locked to an Availability Zone (AZ)
- An EBS Volume in us-east-1a cannot be attached to us-east-1b
- To move a volume across, you first need to snapshot it

Have a provisioned capacity (size in GBs, and IOPS)
- You get billed for all the provisioned capacity
- You can increase the capacity of the drive over time

# 2. EBS Volume Types
EBS Volumes come in 4 types
- GP2 (SSD): General purpose SSD volume that balances price and performance for a wide variety of workloads
- IO1 (SSD): Highest-performance SSD volume for mission-critical **low-latency or highthroughput** workloads
- ST1 (HDD): Low cost HDD volume designed for **frequently accessed, throughputintensive workloads**
- SC1 (HDD): Lowest cost HDD volume designed for **less frequently** accessed workloads

Only **GP2** and **IO1** can be used as **boot volumes**，仅SSD的2中磁盘类型可以用于根卷

## 2.1 GP2

- 1 GiB - 16 TiB
- Small gp2 volumes can burst IOPS to 3000
- Max IOPS is 16,000
- 3 IOPS per GB, means at 5,334GB we are at the max IOPS

Use cases
- Recommended for most workloads
- System boot volumes
- Virtual desktops
- Low-latency interactive apps
- Development and test environments

## 2.2 IO1
- 4 GiB - 16 TiB
- IOPS is provisioned (PIOPS) – MIN 100 - MAX 64,000 (Nitro instances) else MAX 32,000 (other instances)
- The maximum ratio of provisioned IOPS to requested volume size (in GiB) is 50:1

Use cases
- Critical business applications that require sustained IOPS performance, or **more than 16,000 IOPS per volume (gp2 limit)**
- Large **database workloads**, such as: MongoDB, Cassandra, Microsoft SQL Server, MySQL, PostgreSQL, Oracle

## 2.3 ST1
- 500 GiB - 16 TiB
- Max IOPS is 500
- Max throughput of 500 MiB/s – can burst

Use cases
- Streaming workloads requiring consistent, fast throughput at a low price.
- **Big data, Data warehouses, Log processing**
- Apache Kafka
- Cannot be a boot volume

## 2.4 SC1
- 500 GiB - 16 TiB
- Max IOPS is 250
- Max throughput of 250 MiB/s – can burst

Use cases
- Throughput-oriented storage for large volumes of data that is **infrequently accessed**
- Scenarios where the lowest storage cost is important
- Cannot be a boot volume

# 3.  gp2, st1 and sc1 volumes I/O burst
This is a similar concept to t2 instances with their CPU

- If your gp2 volume is **less than 1000 GiB** (means IOPS less than 3000) it can “burst” to 3000 IOPS performance
- You accumulate “burst credit over time”, which allows your volume to have good performance when needed
- The bigger the volume the faster you fill up your “burst credit balance”
- What happens if I empty my I/O credit balance?
  - The maximum I/O you get becomes the baseline you paid for
  - If you see the balance being 0 all the time, increase the gp2 volume or switch to io1
  - Use CloudWatch to monitoring the I/O credit balance

**Note: burst concept also applies to st1 or sc1 (for increase in throughput)**

# 4. Computing MB/s based on IOPS
## 4.1 gp2:
- Throughput in MiB/s = (**Volume** size in **GiB**) × (**IOPS** per **GiB**) × (**I/O** size in **KiB**)
- ex: 300 I/O operations per second * 256 KiB per I/O operation = 75 MiB/s
- Limit to a max of 250 MiB/s (means volume >= 334 GiB won’t increase throughput)

## 4.2 io1:
- Throughput in MiB/s = (Provisioned **IOPS**) × (**I/O** size in **KiB**)
- The throughput limit of io1 volumes is 256 KiB/s for each IOPS provisioned
- Limit to a max of 500 MiB/s (at 32,000 IOPS) and 1000 MiB/s (at 64,000 IOPS)

# 5. EBS Snapshots
- Incremental – only backup changed blocks
- EBS **backups use IO** and you shouldn’t run them while your application is handling a lot of traffic
- Snapshots will be stored in S3 (but you won’t directly see them)
- Not necessary to detach volume to do snapshot, but recommended
- **Max 100,000 snapshots**
- Can copy snapshots across AZ or Region, migrate it to a different AZ (or region)
- Can make Image (AMI) from Snapshot
- ?? EBS volumes restored by snapshots need to be pre-warmed (using fio or dd command to read the entire volume)
- ?? Snapshots can be automated using Amazon Data Lifecycle Manager

# 6. EBS Encryption
- When you create an **encrypted EBS** volume, you get the following:
  - Data at rest is encrypted inside the volume
  - All the data in flight moving between the instance and the volume is encrypted
  - All snapshots are encrypted
  - All volumes created from the snapshot are encrypted

- Encryption and decryption are handled transparently (you have nothing to do)
- **Encryption has a minimal impact on latency**
- EBS Encryption leverages keys from **KMS (AES-256)**
- Copying an unencrypted snapshot allows encryption

# 7. EBS vs Instance Store
- Some instance do not come with Root EBS volumes, Instead, they come with “Instance Store” (= ephemeral storage)
- **Instance store is physically attached to the machine** (EBS is a network drive)

Pros:
- **Better I/O performance**
- Good for buffer / cache / scratch data / temporary content
- Data survives reboots

Cons:
- On stop or termination, the instance store is lost
- You can’t resize the instance store
- Backups must be operated by the user


# 8. CloudWatch and EBS
- Important EBS Volume CloudWatch metrics:
  - VolumeIdleTime: number of seconds when no read / write is submitted
  - VolumeQueueLength: number of operations waiting to be executed. High number means probably an IOPS or application issue
  - BurstBalance: if it becomes 0, we need a volume with more IOPS
- gp2 volume types: 5 minutes interval
- io1 volume types: 1 minute interval
- EBS volumes have status check:
  - Ok -The volume is performing good.
  - Warning - Perfomance below expected.
  - Impaired受损的 - Stalled熄火抛锚, performance severly degraded.
  - Insufficient-data - metric data collecetion in progress.

# 9. EFS – Elastic File System
- **Compatible with Linux based AMI (not Windows)**
- Managed NFS (**network file system**) that can be **mounted on many EC2**
  - EFS mounting 100s of instances
- EFS works with EC2 instances in **multi-AZ**
- Highly available, scalable, expensive (3x gp2), pay per use

- Use cases: content management, web serving, data sharing, Wordpress
- Uses NFSv4.1 protocol
- Uses security group to control access to EFS
- Performance mode:
  - General purpose (default)
  - Max I/O – used when thousands of EC2 are using the EFS
- ?? EFS file sync to sync from on-premise file system to EFS
- Backup EFS-to-EFS (incremental – can choose frequency)
- Encryption at rest using KMS





















































