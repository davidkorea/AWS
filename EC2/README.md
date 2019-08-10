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




