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
![](https://i.loli.net/2019/08/22/uQFa8UXRgfbCMjs.png)

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


# 3. Secondary Index

![](https://i.loli.net/2019/08/22/gXUq532BNY9V8wW.png)

## 3.1 LSI (Local Secondary Index)
![](https://i.loli.net/2019/08/22/NLDJFm9vlAaWsoK.png)
- LSI = **Partition Key + Optional Sort Key**
- The **Partition Key** should be the **same as the origin** Partition Key, also LSI should have a **Sort Key**
- Alternate range key for your table, **local to the hash key**
- **Up to five** local secondary indexes per table.
- The sort key consists of exactly one scalar attribute.
- The attribute that you choose must be a scalar String, Number, or Binary
- **LSI must be defined at table creation time**

![](https://i.loli.net/2019/08/22/A3pNK8qgShm7kja.png)

## 3.2 GSI (Global Secondary Index)
![](https://i.loli.net/2019/08/22/dbEcGj1KqAaJUkn.png)
- GSI Partition Key be ether same as origin or not, can have no Sort Key
- To **speed up queries** on **non-key attributes**(除了主键之外的其它列), use a Global Secondary Index
- ~~GSI = **Partition Key + Optional Sort Key**~~
- The index is a **new “table”** and we can project attributes on it
- The partition key and sort key of the original table are always projected (KEYS_ONLY)
- Can specify extra attributes to project (INCLUDE)
- Can use all attributes from main table (ALL)
- **MUST define RCU / WCU** for the index
- Possibility to add / modify GSI (not LSI)

<p align="center">
    <img src="https://i.loli.net/2019/08/22/A62aNKJPmW1QcZv.png"  width="400" height="200">
    <img src="https://i.loli.net/2019/08/22/iPLCRoYBrZVk5SH.png"  width="400" height="200">
</p>

## 3.3 Indexes and Throttling
- GSI:
  - If the **writes are throttled on the GSI**, then the **main table will be throttled!** as well Even if the WCU on the main tables are fine
  - Choose your GSI partition key carefully!
  - Assign your WCU capacity carefully!
- LSI:
  - Uses the WCU and RCU of the main table
  - No special throttling considerations


# 4. Provisioned Throughput

![](https://i.loli.net/2019/08/22/XQw4UhGd8ZgjYWo.png)

- Table **MUST** have **provisioned Read and Write Capacity Units**, by default.
  - **Read Capacity Units (RCU)**: throughput for reads ($0.00013 per RCU)
    - 1 RCU = **1 strongly** consistent read of **4 KB** per **second**
    - 1 RCU = **2 eventually** consistent read of **4 K**B per **second**   
      > - Example 1: 10 strongly consistent reads per seconds of 4 KB each
      >   - We need 10 * 4 KB / 4 KB = 10 RCU
      > - Example 2: 16 eventually consistent reads per seconds of 12 KB each
      >   - We need (16 / 2) * ( 12 / 4 ) = 24 RCU
      > - Example 3: 10 strongly consistent reads per seconds of 6 KB each
      >   - We need 10 * 8 KB / 4 = 20 RCU (we have to round up 6 KB to 8 KB)

  - **Write Capacity Units (WCU)**: throughput for writes ($0.00065 per WCU)
    - 1 WCU = **1 write** of **1 KB** per **second**    
      > - Example 1: we write 10 objects per seconds of 2 KB each.
      >   - We need 2 * 10 = 20 WCU
      > - Example 2: we write 6 objects per second of 4.5 KB each
      >   - We need 6 * 5 = 30 WCU (4.5 gets rounded to the upper KB)
      > - Example 3: we write 120 objects per minute of 2 KB each
      >   - We need 120 / 60 * 2 = 4 WCU

- **Option to setup auto-scaling** of throughput to meet demand
- Throughput can be exceeded temporarily using “**burst credit**”
- If burst credit are empty, you’ll get a “ProvisionedThroughputException”.
- It’s then advised to do an exponential back-off retry

![](https://i.loli.net/2019/08/22/hA5RZYufWD8V1ry.png)
![](https://i.loli.net/2019/08/22/Fgfa3ZqC9Dmzx4t.png)


# 5. Partitions Internal
- Data is divided in partitions
  - Partition keys go through a hashing algorithm to know to which partition they go to
- To compute the number of partitions:
  - By capacity: (TOTAL RCU / 3000) + (TOTAL WCU / 1000)
  - By size: Total Size / 10 GB
  - Total partitions = CEILING(MAX(Capacity, Size))
- **WCU and RCU are spread evenly均等地 between partitions**


















































































