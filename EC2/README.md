# EC2

- **stop， restart EC2 instance will not lose Private IPv4.**
    ![](https://i.loli.net/2019/08/10/fDc3owzHSvWJByP.png)
    
# 1. Placement Group
    - Cluster, SAME AZ and SAME Hardware
        - NOt all types of EC2 can be placed in a Cluster placement group, eg t2.micra cannot
    - Partition, SAME AZ but different Hardware, max 7 Partition(hardware/host)
    - Spread, different AZ, and each AZ has max 7 instance 

# 2. Termination Protection
- Stop行为=Terminate & 启用终止保护 
    - 仍然可以通过SSH到该EC2，执行`shutdown now`命令，从而将EC2终止掉
    - 通过console到关闭功能无法终止
        ![](https://i.loli.net/2019/08/10/IiGCw4LZm5kOVRS.png)
    - We have an instance where shutdown behavior = terminate and enable terminate protection is ticked• We shutdown the instance from the OS, what will happen ?• The instance will still be terminated!
    
# 3. EC2 Launch Troubleshooting
### InstanceLimitExceeded error

if you get this error, it means that you have reached your limit of max number of instances per region, Default Limit of Instances in each region: 20.

request to AWS to increase your limit of the region.
![](https://i.loli.net/2019/08/10/PAjTno3UWiwmaz7.png)

### InsufficientInstanceCapacity 

it means AWS does not have that much On-Demand capacity in the particular AZ to which the instance is launched.

- Wait for few mins before requesting again.
- If urgent, submit a request for a different instance type now, which can be resized later.

### Instance Terminates Immediately (goes from pending to terminated)

- You've reached your EBS volume limit.
- An EBS snapshot is corrupt.
- The root EBS volume is encrypted and you do not have permissions to access the KMS key for decryption.
- The instance store-backed AMI that you used to launch the instance is missing a required part (an image.part.xx file).

To find the exact reason, check out the EC2 console of AWS - instances - Description tab, note the reason next to the State transition reason label. 状态转换原因,状态转换消息

![](https://i.loli.net/2019/08/10/xGo7NgARjHSdeab.png)

# 4. EC2 SSH trouble
- Make sure the private key (pem file) on your linux machine has 400 permissions, else you will get “Unprotected Private Key File error”
    - if you chown 755, 权限太高，也允许，只能使用400
- Make sure the username（ec2-user） for the OS is given correctly when logging via SSH, else you will get “Host key not found” error.
- Possible reasons for ‘connection timeout’ to EC2 instance via SSH : 
    - SG is not configured correctly.
    - CPU load of the instance is high

# 5. EC2 Instance LaunchTypes

- On Demand Instances: short workload, predictable pricing
    - Has the highest cost but no upfront payment
- Reserved Instances: long workloads (>= 1 year)
    - Up to 75% discount compared to On-demand
    - Pay upfront for what you use with long term commitment
    - Reservation period can be 1 or 3 years
    - Reserve a specific instance type
- Convertible Reserved Instances: long workloads with flexible instances
    - can change the EC2 instance type 
    - Up to 54% discount
- Scheduled Reserved Instances: launch within time window you reserve
    - launch within time window you reserve
    - When you require a fraction of day / week / month
- Spot Instances: short workloads, for cheap, can lose instances
    - Can get a discount of up to 90% compared to On-demand
    - You bid a price and get the instance as long as its under the price
    - Spot instances are reclaimed with a 2 minute notification warning when the spot price goes above your bid
- Dedicated Hosts: book an entire physical server, control instance placement
    - Physical dedicated EC2 server for your use
    - Full control of EC2 Instance placement
    - Visibility into the underlying sockets / physical cores of the hardware
    - Allocated for your account for a 3 year period reservation
    - Useful for software that have complicated licensing model (BYOL – Bring Your Own License)
- Dedicated Instances: no other customers will share your hardware
    - Instances running on hardware that’s dedicated to you
    - May share hardware with other instances in same account
    - No control over instance placement (can move hardware after Stop / Start)

# 6. EC2 InstanceTypes – Main ones
- R: applications that needs a lot of RAM – in-memory caches
- C: applications that needs good CPU – compute / databases
- M: applications that are balanced (think “medium”) – general / web app • I: applications that need good local I/O (instance storage) – databases
- G: applications that need a GPU – video rendering / machine learning
- T2 / T3: burstable instances (up to a capacity) 
    - When the machine needs to process something unexpected (a spike in load for example), it can burst, and CPU can be VERY good.
    - If the machine bursts, it utilizes “burst credits”
    - If all the credits are gone, the CPU becomes BAD
    - If the machine stops bursting, credits are accumulated over time
- T2 / T3 - unlimited: unlimited burst
    - You pay extra money if you go over your credit balance, but you don’t lose in performance
    
# 7. AMI
### 7.1 Share an AMI
You can share an AMI with another AWS account.
- Sharing an AMI does not affect the ownership of the AMI.
### 7.2 Copy an AMI 
- If you copy an AMI that has been shared with your account, you are the owner of the target AMI in your account.

- To copy an AMI that was shared with you from another account, the owner of the source AMI must grant you read permissions for the storage that backs the AMI, either the associated EBS snapshot (for an Amazon EBS-backed AMI) or an associated S3 bucket (for an instance store-backed AMI).
- You can't copy an encrypted AMI that was shared with you from another account.
    - Instead,if the underlying snapshot and encryption key were shared with you, you can copy the snapshot while re- encrypting it with a key of your own.You own the copied snapshot, and can register it as a new AMI.
    - 可以先使用比人的AMI创建一个EC2，在将这个EC2创建一个AMI，这个AMI就属于你自己了
- You can't copy an AMI with an associated **billingProduct** code that was shared with you from another account.This includes Windows AMIs and AMIs from the AWS Marketplace.To copy a shared AMI with a billingProduct code, launch an EC2 instance in your account using the shared AMI and then create an AMI from the instance.

> You have taken a snapshot of an encrypted EBS volume and would like to share the snapshot with another AWS account. Which statements are true about sharing snapshots of encrypted EBS volumes? 
> - A custom CMK key must be used for encryption if you want to share the snapshot    
> - You must share the CMK key as well as the snapshot with the other AWS account    


# 8. CloudWatch Metrics for EC2

### 8.1 EC2 included metrics
- CPU: CPU Utilization + Credit Usage / Balance
- Network: Network In / Out
- Status Check:
- Instance status = check the EC2 VM
- System status = check the underlying hardware
- Disk: Read / Write for Ops / Bytes ( **Instance Store ONLY**, EBS based EC2 has no Disk metric)

### 8.2 EC2 Custom Metrics
Sample custom metrics for EC2: 
- RAM usage
- Swap usage
- Any custom metric for your application (requests per seconds, etc.. )


















































