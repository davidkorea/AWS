
# 1. RDS Read Replicas
- Up to 5 Read Replicas, 
- Within AZ, **Multi-AZ**, Cross AZ or Cross Region
- Replication is **ASYNC**, so reads are eventually consistent
- Replicas can be promoted to their own DB，只读副本可以升级为Master读写DB
- Applications must update the connection string to leverage read replicas，独立于Master之外的单独ARN
