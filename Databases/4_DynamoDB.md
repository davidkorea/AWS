- Fully Managed, Highly available with replication across 3 AZ
- Stored in SSD
- Integrated with IAM for security, authorization and administration
- Enables event driven programming with DynamoDB Streams

# 1. DynamoDB - Basics
- DynamoDB is made of **tables**
- Each table has a **primary key** , must be decided **at creation time**，创建表时，指定主键
- Each table can have an **infinite number of items(rows)**，每个表可无数多行
  - Each item has **attributes can be added over time – can be null**，每一行的属性可以随用随加
  - Maximum size of a item is **400KB**，每一行最大400Kb
- Data types supported are:
  - Scalar Types: **String, Number, Binary, Boolean, Null**
  - Document Types: **List, Map**
  - Set Types: **String Set, Number Set, Binary Set**

# 2. Primary Keys
![](https://i.loli.net/2019/08/22/fY9VwnpCT3O1UQB.png)

## 2.1 Partition Key ONLY (HASH)
- Partition key must be **unique for each item**，每一行记录的Partition Key需要唯一，和其它行不能重复
- Partition key must be “diverse” so that the data is distributed
- Example: user_id for a users table

<p align="center">
    <img src="https://i.loli.net/2019/08/22/EiP8hn7UVqGKZuR.png"  width="400" height="200">
</p>

## 2.2 Partition Key + Sort Key
- The **combination(Partition Key + Sort Key) must be unique**
- Data is grouped by partition key
- Sort key == range key
- Example: users-games table
  - user_id for the partition key
  - game_id for the sort key
  
<p align="center">
    <img src="https://i.loli.net/2019/08/22/5jnIlO9JCg1pkK4.png"  width="400" height="200">
</p>

# 3. Provisioned Throughput

![](https://i.loli.net/2019/08/22/XQw4UhGd8ZgjYWo.png)

- Table **must** have **provisioned read and write capacity units**, by default.
  - **Read Capacity Units (RCU)**: throughput for reads ($0.00013 per RCU)
    - 1 RCU = **1 strongly** consistent read of **4 KB** per **second**
    - 1 RCU = **2 eventually** consistent read of **4 K**B per **second**
  - **Write Capacity Units (WCU)**: throughput for writes ($0.00065 per WCU)
    - 1 WCU = **1 write** of **1 KB** per **second**
    
- **Option to setup auto-scaling** of throughput to meet demand
- Throughput can be exceeded temporarily using “**burst credit**”
- If burst credit are empty, you’ll get a “ProvisionedThroughputException”.
- It’s then advised to do an exponential back-off retry

![](https://i.loli.net/2019/08/22/hA5RZYufWD8V1ry.png)
![](https://i.loli.net/2019/08/22/Fgfa3ZqC9Dmzx4t.png)





















































































