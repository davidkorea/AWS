# Scaling Processes in ASG
- Launch: Add a new EC2 to the group, increasing the capacity
- Terminate: Removes an EC2 instance from the group, decreasing its capacity.
- HealthCheck: Checks the health of the instances
- ReplaceUnhealthy: Terminate unhealthy instances and re-create them
- AZRebalance: Balancer the number of EC2 instances across AZ
- AlarmNotification: Accept notification from CloudWatch
- ScheduledActions: Performs scheduled actions that you create.
- AddToLoadBalancer: Adds instances to the load balancer or target group

We can suspend暂停 these processes!
- AZRebalance = launch new instance then terminate old instance

- If you suspend the Launch process
  - AZRebalance won’t launch instances
  - AZRebalance won’t terminate instances

- If you suspend the Terminate process
  - The ASG can grow up to 10% of this size (it’s allowed during rebalances)
  - The ASG could remain at the increased capacity as it can’t terminate instances

![](https://i.loli.net/2019/08/09/5K9mUYHiyODQjhS.png)
