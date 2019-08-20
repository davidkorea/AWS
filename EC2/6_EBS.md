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




































































