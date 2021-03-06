- Fully Managed, **Highly available** with replication across **3 AZ**
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
    <img src="https://i.loli.net/2019/08/22/EiP8hn7UVqGKZuR.png"  width="400" height="160">
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
![](https://i.loli.net/2019/08/22/SP1t5WIhFeL37VG.png)

## 3.1 LSI (Local Secondary Index)

<p align="center">
    <img src="https://i.loli.net/2019/08/22/NLDJFm9vlAaWsoK.png"  width="400" height="250">
</p>

- LSI = **Partition Key + Optional Sort Key**
- The **Partition Key** should be the **same as the origin** Partition Key(base table), also LSI should have a **Sort Key**
- Alternate range key for your table, **local to the hash key**
- **Up to five** local secondary indexes per table.
- The sort key consists of exactly one scalar attribute.
- The attribute that you choose must be a scalar String, Number, or Binary
- **LSI must be defined at table creation time**

<p align="center">
    <img src="https://i.loli.net/2019/08/22/A3pNK8qgShm7kja.png"  width="800" height="250">
</p>


## 3.2 GSI (Global Secondary Index)

<p align="center">
    <img src="https://i.loli.net/2019/08/22/NmD3YQyB8IPWLrk.png"  width="400" height="250">
</p>

![](https://i.loli.net/2019/08/22/KLXWy6ftY3UBTwJ.png)

- GSI Partition Key be ????????ether same as origin or not, can have no Sort Key
- **MUST define RCU / WCU** for the index
- To **speed up queries** on **non-key attributes**(除了主键之外的其它列), use a Global Secondary Index
- ~~GSI = **Partition Key + Optional Sort Key**~~
- The index is a **new “table”** and we can project attributes on it
- The partition key and sort key of the original table are always projected (KEYS_ONLY)
- Can specify extra attributes to project (INCLUDE)
- Can use all attributes from main table (ALL)
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

- On Demand (new from Nov 2018)
  - No capacity planning needed (WCU / RCU) – **scales automatically**
  - **2.5x more expensive than provisioned** capacity (use with care)**
  - Helpful when spikes are un-predictable or the application is very low throughput

![](https://i.loli.net/2019/08/22/hA5RZYufWD8V1ry.png)
![](https://i.loli.net/2019/08/22/Fgfa3ZqC9Dmzx4t.png)

# 5. DynamoDB Streams

类似event log，记录各种数据库各种变动事件

<p align="center">
    <img src="https://i.loli.net/2019/08/22/5m9LeDIpGUuCfoK.png"  width="280" height="400">
</p>

- Changes in DynamoDB (Create, Update, Delete) can end up in a DynamoDB Stream
- **DynamoDB triggers connect DynamoDB streams to Lambda functions**
- This stream can be read by AWS Lambda, and we can then do:
  - React to changes in real time (welcome email to new users)
  - Analytics
  - Create derivative tables / views
  - Insert into ElasticSearch
- Could **implement cross region replication using Streams**
- Stream has 24 hours of data retention

![](https://i.loli.net/2019/08/22/4WEpTD7Nz1kdtlM.png)
![](https://i.loli.net/2019/08/22/7VP6cHxneWisr2d.png)
![](https://i.loli.net/2019/08/22/NwjWt4VsIabSF35.png)
![](https://i.loli.net/2019/08/22/eRLhjUXSZkQxuBn.png)
![](https://i.loli.net/2019/08/22/bjGTREg6CPJpade.png)
![](https://i.loli.net/2019/08/22/PhyuONnjCgxfJ1S.png)
![](https://i.loli.net/2019/08/22/CtAb5IBGQzveJ93.png)
![](https://i.loli.net/2019/08/22/TEDByjU1WKGl5fu.png)


# 6. DynamoDB Accelerator - DAX
<p align="center">
    <img src="https://i.loli.net/2019/08/22/m5oNl7gPb1uQenG.png"  width="320" height="400">
</p>

- DAX = DynamoDB Accelerator
- Seamless **cache for DynamoDB**, no application rewrite
- **Writes go through DAX** to DynamoDB
- Micro second latency for cached reads & queries
- **Solves the Hot Key problem (too many reads)**
- **5 minutes TTL for cache by default**
- Up to 10 nodes in the cluster
- Multi AZ (3 nodes minimum recommended for production)
- Secure (Encryption at rest with KMS, VPC, IAM, CloudTrail…)

![](https://i.loli.net/2019/08/22/KF65Bkc1HdeOrxS.png)
![](https://i.loli.net/2019/08/22/tTZqNadEhpwQlB3.png)


# 7. Partitions Internal
- You **start** with **one** partition
- Each partition:
  - Max of 3000 RCU / 1000 WCU
  - Max of 10GB
- Data is divided in partitions
  - Partition keys go through a hashing algorithm to know to which partition they go to
- To compute the number of partitions:
  - By capacity: (TOTAL RCU / 3000) + (TOTAL WCU / 1000)
  - By size: Total Size / 10 GB
  - Total partitions = CEILING(MAX(Capacity, Size))
- **WCU and RCU are spread evenly均等地 between partitions**
  ![image](http://wx1.sinaimg.cn/large/006gDTsUgy1g6l836fl5sj30hr0eamyi.jpg)


# 8. DynamoDB – Batching 
## 8.1 Writes
- **`BatchWriteItem`**
  - Up to **25 PutItem** and / or **DeleteItem** in one call
  - Up to **16 MB** of data written
  - Up to **400 KB** of data per item, default limit is 400 Kb per item
- Batching allows you to save in latency by reducing the number of API calls done against DynamoDB
- Operations are done in parallel for better efficiency
- It’s possible for part of a batch to fail, in which case we have the try the failed items (using exponential back-off algorithm)

## 8.2 Reads
- **`BatchGetItem`**
  - **Up to 100 items**
  - Up to **16 MB** of data
  - Items are retrieved in parallel to minimize latency


# 9. DynamoDB – Query
- Query returns items based on:
  - **PartitionKey** value (must be **=** operator)
  - **SortKey** value (**=, <, <=, >, >=, Between, Begin**) – optional
  - FilterExpression to further filter (client side filtering)
  ![image](http://ws3.sinaimg.cn/mw690/006gDTsUgy1g6l8hhhr5rj30ot0duq5r.jpg)
  
- Returns:
  - Up to 1 MB of data
  - Or number of items specified in Limit
- Able to do pagination on the results
- Can query table, a local secondary index, or a global secondary index

# 10. DynamoDB - Scan
- **Scan the entire table** and then filter out data (inefficient)
  - 每次查看一张表，都是scan操作
- Returns up to 1 MB of data – use pagination to keep on reading
- Consumes a lot of RCU
- Limit impact using Limit or reduce the size of the result and pause
- For faster performance, use parallel scans:
  - Multiple instances scan multiple partitions at the same time
  - Increases the throughput and RCU consumed
  - Limit the impact of parallel scans just like you would for Scans
- Can use a ProjectionExpression + FilterExpression (no change to RCU)

![image](http://wx2.sinaimg.cn/mw690/006gDTsUgy1g6l8lkwrx9j30op0bqgny.jpg)


# 11. DynamoDB -TTL (Time to Live)
- TTL = automatically delete an item after an expiry date / time
- TTL is provided at no extra cost, **deletions do not use WCU / RCU**
- TTL is a background task operated by the DynamoDB service itself
- Helps reduce storage and manage the table size over time
- Helps adhere to regulatory norms
- **TTL is enabled per row **
  - **define a TTL column, and add a date there**
    ![image](http://wx1.sinaimg.cn/mw690/006gDTsUgy1g6l9t68xcpj30vn076jsc.jpg)
    ![image](http://wx4.sinaimg.cn/mw690/006gDTsUgy1g6l9vcytc3j30ka0dln19.jpg)
- DynamoDB typically **deletes expired items** within **48 hours of expiration**
- Deleted items due to TTL are **also deleted in GSI / LSI**
- DynamoDB **Streams can help recover expired items**
- https://www.epochconverter.com/
  ![image](http://wx3.sinaimg.cn/mw690/006gDTsUgy1g6l9rfifkrj30kw0h13zz.jpg)
























# 8. DynamoDB Concurrency并发性
- DynamoDB has a feature called “Conditional Update / Delete”
- That means that you can ensure an item hasn’t changed before altering it
- That makes DynamoDB an optimistic locking / concurrency database

当满足某个条件时，才进行更新操作
<p align="center">
    <img src="https://i.loli.net/2019/08/22/hKxowa7EH2kMAjV.png"  width="700" height="150">
</p>



  
# 10. DynamoDB Transactions处理交易
- New feature from November 2018，同时在多个表中 写入/更新/删除 多条记录row
- Transaction = Ability to Create / Update / Delete **multiple rows** in **different tables** at the same time
  - Include **up to 10 unique items** or **up to 4 MB of data**
- It’s an “all or nothing” type of operation.
- Write Modes: Standard, Transactional
- Read Modes: Eventual Consistency, Strong Consistency, Transactional
- Consume 2x of WCU / RCU

<p align="center">
    <img src="https://i.loli.net/2019/08/22/4dlK6T1WE3qB9RD.png"  width="700" height="300">
</p>


# 11. Global Tables
- Multi region, fully replicated, high performance
- To create a global table, ensure that this **table is empt**y and that **DynamoDB Streams are enabled**.
  
![](https://i.loli.net/2019/08/22/BhKNVAdE8MpjXD5.png)

# 12. Security & Other Features
- Security:
  - **VPC Endpoints available** to access DynamoDB without internet
  - Access fully controlled by IAM
  - Encryption at rest using KMS
  - Encryption in transit using SSL / TLS
- Backup and Restore feature available
  - **Point in time restore like RDS**
  - No performance impact

- **Amazon Database Migration Service(DMS)** can be used to migrate **from Mongo, Oracle, MySQL, S3, etc… to DynamoDB**
- You can **launch a local DynamoDB on your computer** for development purposes
- DynamoDB CLI – Good to Know
  - --projection-expression : attributes to retrieve
  - --filter-expression : filter results
- General CLI pagination options including DynamoDB / S3:
  - Optimization:
    - `--page-size` : full dataset is still received but each API call will request less data (helps avoid timeouts)
  - Pagination:
    - `--max-items` : max number of results returned by the CLI. Returns NextToken
    - `--starting-token`: specify the last received NextToken to keep on reading


































































