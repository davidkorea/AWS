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
