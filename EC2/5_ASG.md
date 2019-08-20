

- **Reactive Event-based scale:（被动）事件驱动伸缩，ELB的根据alarm polic的自动伸缩**
- **Proactive cyclic scale：主动循环伸缩，提前主动计划的伸缩schedule**
- **ASG + ELB**
  ![](https://i.loli.net/2019/08/20/ChMuXza61seLcGl.png)
  
-----

- Scaling policies can be on CPU, Network… and can even be on custom **metrics** or based on a **schedule** (if you know your visitors patterns)
  ![](https://i.loli.net/2019/08/20/n9WCOvNDbepfikz.png)
- ASGs use Launch configurations and you update an ASG by providing a new launch configuration
- IAM roles attached to an ASG will get assigned to EC2 instances，所有ASG下的EC2将继承此IAM Role
- ASG are free. You pay for the underlying resources being launched
- ASG can terminate instances marked as unhealthy by an LB (and hence replace them)
- To make sure you have high availability, means you have least 2 instances running across 2 AZ in your ASG (must configure multi AZ ASG)
- Health checks available:
  - EC2 Status Checks
  - ELB Health Checks
- Good to know CLI:
  - set-instance-health
  - terminate-instance-in-auto-scaling-group
  
# 1. ASG attributes
- launch configuration
  - AMI + Instance Type
  - EC2 User Data
  - EBS Volumes
  - Security Groups
  - SSH Key Pair
- Min Size / Max Size / Initial Capacity
- Network + Subnets Information
- Load Balancer Information
- Scaling Policies

# 2. Auto Scaling Alarms
- It is possible to scale an ASG based on CloudWatch alarms, An Alarm monitors a metric (such as Average CPU)
- Metrics are computed for the overall ASG instances
  - We can create scale-out policies (increase the number of instances)
  - We can create scale-in policies (decrease the number of instances)
- It is now possible to define ”better” auto scaling rules that are directly managed by EC2, These rules are easier to set up and can make more sense
  - Target Average CPU Usage
  - Number of requests on the ELB per instance
  - Average Network In
  - Average Network Out
- We can auto scale based on a **custom metric** (ex: number of connected users)

# 3. Scaling Processes in ASG
- Launch: Add a new EC2 to the group, increasing the capacity
- Terminate: Removes an EC2 instance from the group, decreasing its capacity.
- HealthCheck: Checks the health of the instances
- ReplaceUnhealthy: Terminate unhealthy instances and re-create them
- **AZRebalance: Balancer the number of EC2 instances across AZ**
- AlarmNotification: Accept notification from CloudWatch
- ScheduledActions: Performs scheduled actions that you create.
- AddToLoadBalancer: Adds instances to the load balancer or target group
- We can suspend these processes!

AZRebalance
- AZRebalance = launch new instance then terminate old instance
- If you suspend the Launch process
  - AZRebalance won’t launch instances
  - AZRebalance won’t terminate instances
- If you suspend the Terminate process
  - The ASG can grow up to 10% of this size (it’s allowed during rebalances)
  - The ASG could remain at the increased capacity as it can’t terminate instances
  
![](https://i.loli.net/2019/08/09/5K9mUYHiyODQjhS.png)


# 4. Troubleshooting ASG issues
- xx instance(s) are already running. Launching EC2 instance failed.
  - The Auto Scaling group has reached the limit set by the DesiredCapacity parameter. Update your Auto Scaling group by providing a new value for the **desired capacity**.
- Launching EC2 instances is failing:
  - The security group does not exist. SG might have been deleted
  - The key pair does not exist. The key pair might have been deleted
- If the ASG fails to launch an instance for over 24 hours, it will automatically suspend the processes (administration suspension)


# 5. ASG Default Termination Policy
(simplified version):
1. Find the AZ which has the most number of instances
2. If there are multiple instances in the AZ to choose from, delete the one with the oldest launch configuration

-  ASG tries the balance the number of instances across AZ by default

# 6. Scaling Cooldowns
- The cooldown period helps to ensure that your Auto Scaling group doesn't launch or terminate additional instances before the previous scaling activity takes effect.
- If your application is scaling up and down multiple times each hour, modify the Auto Scaling Groups cool-down timers and the CloudWatch Alarm Period that triggers the scale in




































































