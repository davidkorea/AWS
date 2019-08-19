<p align="center">
    <img src="https://i.loli.net/2019/08/19/vrUC1haXf2ZN6dJ.png"  width="300" height="250">
</p>

- We need to install the **SSM agent** onto the systems we control, **can be on-premise servers**
  - Installed by default on Amazon Linux AMI & some Ubuntu AMI
  - If an instance can’t be controlled with SSM, it’s probably an issue with the SSM agent!
  
- Make sure the EC2 instances have a proper **IAM role** to allow SSM actions, SSM can create a default role for simply using.
- add exsiting EC2 instances to SSM by **Quick Setup**


![](https://i.loli.net/2019/08/19/Ec7s5aVmp2DYRLw.png)
# 1. Resource Groups
对EC2进行分组
- Create, view or manage logical group of resources thanks to tags
  - You can add Text Key Value Pairs called Tags to many AWS resources, Commonly used in EC2
  - Free naming, common tags are: Name, Environment, Team …
  - They’re used for
    - Resource grouping
    - Automation
    - Cost allocation
  - Better to have too many tags than too few!
- Allows creation of logical groups of resources such as
  - Applications
  - Different layers of an application stack
  - Production versus development environments

# 2. Documents
所有功能都是通过执行Documents（文档）来实现,如批量执行命令，打补丁patching
- Documents can be in JSON or YAML
- You define parameters
- You define actions
- Many documents already exist in AWS

<p align="center">
    <img src="https://i.loli.net/2019/08/19/hXqB1J4PeMs5DY9.png"  width="550" height="300">
</p>


# 3. Run Command
- Execute a **Document** (= script) or just run a command across multiple instances (using resource groups)
- Rate Control / Error Control
- Integrated with IAM & CloudTrail
- No need for SSH
- Results in the console

# 4. PATCH
- Inventory => List software on an instance，已安装软件的清单
- Inventory + Run Command => Patch Software，对安装的软件进行补丁
- Patch manager + Maintenance Window => Patch OS，对系统进行补丁
- Patch manager => Gives you compliance
- State manager => Ensure instances are in a consistent state (compliance)


# 5. Session Manager
- Session Manager allows you to start a secure shell on your VM
    - **Does not use SSH** access and bastion hosts
    - **Only EC2** for now, but On Premise soon
    - run with **ssm-user** account instead of ec2-user
- **Log actions** done through secure shells to **S3** and **CloudWatch Logs**
- IAM permissions: access SSM + write to S3 + write to CloudWatch
- CloudTrail can intercept StartSession events
- AWS Secure Shell versus SSH:
    - **No need to open the port 22 at all anymore (security)**
    - **No need for bastion hosts**
    - **All commands are logged to S3 / CloudWatch (auditing)**
    - **Access to Secure Shell is done through User IAM, not SSH keys**


![](https://i.loli.net/2019/08/19/zJum7hCv5c1ATnD.png)
![](https://i.loli.net/2019/08/19/95ErlD7ahNxkQPT.png)
![](https://i.loli.net/2019/08/19/QmVeAFEJMdjgyTU.png)

# 6. AWS Parameter Store


# 7. Lost SSH key for EC2?
- (Traditional method) If the instance is EBS backed:
    - Stop the instance, detach the root volume
    - Attach the root volume to another instance as a data volume
    - Modify the ~/.ssh/authorized_keys file with your new key
    - Move the volume back to the stopped instance
    - Start the instance and you can SSH into it again
- (New method) If the instance is EBS:
    - Run the AWSSupport-ResetAccess automation document in SSM
- Instance Store backed EC2:
    - You can’t stop the instance (otherwise data is lost) – AWS recommends termination
    - My tip: Use Session Manager access and edit the ~/.ssh/authorized_keys file directly

![](https://i.loli.net/2019/08/19/KIdbDMRYhrgJWpj.png)




























































