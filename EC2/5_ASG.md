- Scaling policies can be on CPU, Network… and can even be on custom **metrics** or based on a **schedule** (if you know your visitors patterns)
- ASGs use Launch configurations and you update an ASG by providing a new launch configuration
- IAM roles attached to an ASG will get assigned to EC2 instances，所有ASG下的EC2将继承此IAM Role
- ASG are free. You pay for the underlying resources being launched
- Having instances under an ASG means that if they get terminated for whatever reason, the ASG will restart them. Extra safety!
- ASG can terminate instances marked as unhealthy by an LB (and hence replace them)

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
- AZRebalance: Balancer the number of EC2 instances across AZ
- AlarmNotification: Accept notification from CloudWatch
- ScheduledActions: Performs scheduled actions that you create.
- AddToLoadBalancer: Adds instances to the load balancer or target group
- We can suspend these processes!













































































