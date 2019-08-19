<p align="center">
    <img src="https://i.loli.net/2019/08/19/vrUC1haXf2ZN6dJ.png"  width="250" height="250">
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
- Inventory => List software on an instance
- Inventory + Run Command => Patch Software
- Patch manager + Maintenance Window => Patch OS
- Patch manager => Gives you compliance
- State manager => Ensure instances are in a consistent state (compliance)









































































