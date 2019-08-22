- Fully Managed, Highly available with replication across 3 AZ
- Stored in SSD
- Integrated with IAM for security, authorization and administration
- Enables event driven programming with DynamoDB Streams

# 1. DynamoDB - Basics
- DynamoDB is made of **tables**
- Each table has a **primary key** , must be decided **at creation time**
- Each table can have an **infinite number of items(rows)**，无数多行
  - Each item has **attributes can be added over time – can be null**，每一行的属性可以随用随加
  - Maximum size of a item is **400KB**，每一行最大400Kb
- Data types supported are:
  - Scalar Types: **String, Number, Binary, Boolean, Null**
  - Document Types: **List, Map**
  - Set Types: **String Set, Number Set, Binary Set**
