# 1. Scaling Processes in ASG
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

# 2. ASG for SysOps

- To make sure you have high availability, means you have least 2 instances running across 2 AZ in your ASG (must configure multi AZ ASG)
  ![](https://i.loli.net/2019/08/09/PdWJ1xCAusbmEvH.png)
- Health checks available:
  - EC2 Status Checks
  - ELB Health Checks
- ASG will launch a new instance after terminating an unhealthy one
- ASG will not reboot unhealthy hosts for you
- Good to know CLI:
  - set-instance-health
  - terminate-instance-in-auto-scaling-group


# 3. Troubleshooting ASG issues
- <number of instances> instance(s) are already running. Launching EC2 instance failed.
  - The Auto Scaling group has reached the limit set by the DesiredCapacity parameter. Update your Auto Scaling group by providing a new value for the desired capacity.
- Launching EC2 instances is failing:
  - The **security group does not exist**. SG might have been deleted
  - The **key pair does not exist**. The key pair might have been deleted
- If the ASG fails to launch an instance for over 24 hours, it will automatically suspend the processes (administration suspension)
  
  
  
  
