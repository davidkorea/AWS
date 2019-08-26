- **Templates** have to be **uploaded in S3** and then referenced in CloudFormation
- To **update a template**, we **can’t edit** previous ones. We have to **reupload a new version** of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation.
- templates
  - Manual way:
    - Editing templates in the **CloudFormation Designer**
    - Using the console to input parameters, etc
  - Automated way:
    - Editing templates in a **YAML file**
    - Using the AWS CLI (Command Line Interface) to deploy the templates

# 1. Benefits of AWS CloudFormation
- Infrastructure as code
  - **No resources are manually created**, which is excellent for control
  - The **code can be version controlled** for example using **git**
  - **Changes** to the infrastructure are **reviewed through code**
  
- Cost
  - Each resources within the stack is stagged监视 with an identifier so you can easily see how much a stack costs you
  - You can **estimate the costs** of your resources using the CloudFormation template
  - Savings strategy: In Dev, you could automation **deletion of templates at 5 PM and recreated at 8 AM** safely

- Productivity
  - Ability to destroy and re-create an infrastructure on the cloud on the fly
  - Automated generation of Diagram for your templates!
  - Declarative programming (no need to figure out ordering and orchestration)

- Separation of concern
  - create many stacks for many apps, and many layers. Ex:
    - VPC stacks
    - Network stacks
    - App stacks
  
  
# 2. CloudFormation Building Blocks
Templates components:
1. Resources: your AWS resources declared in the template (MANDATORY)
2. Parameters: the dynamic inputs for your template
3. Mappings: the static variables for your template
4. Outputs: References to what has been created
5. Conditionals: List of conditions to perform resource creation
6. Metadata

Templates helpers:
1. References
2. Functions
  
## 2.1 Resources
```yaml
---
Resources:
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      AvailabilityZone: us-east-1a
      ImageId: ami-009d6802948d06e52
      InstanceType: t2.micro
```
- Resources are the **core** of your CloudFormation template (**MANDATORY**)
- Resources are **declared** and can **reference** each other
- AWS figures out creation, updates and deletes of resources for us
- There are over 224 types of resources, NOT all AWS services! You can work around that using AWS Lambda Custom Resources
- Resource types identifiers are of the form: `AWS::aws-product-name::data-type-name`
  
- All the resources can be found here: http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html
  
- Can I create a dynamic amount of resources?
  - No, you can’t. Everything in the CloudFormation template has to be declared. You can’t perform code generation there
  
## 2.2 Parameters
```yaml
---
Parameters:
  SecurityGroupDescription:
    Description: Security Group Description
    Type: String
```
![](https://i.postimg.cc/9MfD11k1/image.png)

```yaml
---
Parameters:
  SSHKey:
    Type: AWS::EC2::KeyPair::KeyName
    Description: Name of an existing EC2 KeyPair to enable SSH access to the instance
```
![](https://i.postimg.cc/DwKcvJmF/image.png)

- Parameters are a way to **provide inputs ON AWS Web UI** to your AWS CloudFormation template
- Some inputs **can not be determined ahead of time**, Ex: **`Type: AWS::EC2::KeyPair::KeyName`**, get the ketpair on your aws account in a region
- Parameters are extremely powerful, controlled, and can prevent errors from happening in your templates thanks to types.
- Make it a Parameter.
  - this CloudFormation resource configuration likely to **change in the future**
  - You **won’t have to re-upload a template** to change its content 
- Reference a Parameter
  - **Parameters** can be used **anywhere in a template**
  - The `Fn::Ref` function can be leveraged to reference parameters, The shorthand for this in YAML is **`!Ref`**
    - can also reference **other elements within the template**
- AWS offers us pseudo parameters in any CloudFormation template
  
  |Reference Value| Example Return Value|
  |-|-|
  |AWS::AccountId| 1234567890|
  |AWS::NotificationARNs|[arn:aws:sns:us-east-1:123456789012:MyTopic]|
  |AWS::NoValue| Does not return a value.|
  |AWS::Region| us-east-2|
  |AWS::StackId|arn:aws:cloudformation:us-east-1:123456789012:stack/MyStack/1c2fa620-982a-11e3-aff7-50e2416294e0|
  |AWS::StackName| MyStack|

## 2.3 Mappings
```yaml
Mappings: 
  RegionMap: 
    us-east-1: 
      HVM64: "ami-0ff8a91507f77f867"
      HVMG2: "ami-0a584ac55a7631c0c"
    us-west-1: 
      HVM64: "ami-0bdb828fd58c52235"
      HVMG2: "ami-066ee5fd4a9ef77f1"

Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap
        - RegionMap
        - !Ref 'AWS::Region'    # AWS::Region is AWS offered pseudo parameter
        - HVM64
      InstanceType: m1.small
```

- Mappings are **fixed variables** within your CloudFormation Template, All the values are hardcoded within the template
- They’re very handy to differentiate between different environments (dev vs prod), regions (AWS regions), AMI types, etc
- **Mappings VS Parameters ?**
  - **Mappings** are great when you **know in advance** all the values that can be taken and that they can be deduced from variables such as
    - Region
    - Availability Zone
    - AWS Account
    - Environment (dev vs prod)
    - Etc…
  - They allow safer control over the template.
  - Use **Parameters** when the values are **really user specific
  
- **`Fn::FindInMap`** Accessing Mapping Values
  - We use Fn::FindInMap to return a named value from a specific key
  - **`!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`**, yaml list starts with`-`
    ```yaml
    ImageId: !FindInMap
        - RegionMap
        - !Ref 'AWS::Region'    // AWS::Region is AWS offered pseudo parameter
        - HVM64
    ```
  
## 2.4 Outputs?
- The Outputs section declares optional **outputs values** that we **can import into other stacks** (if you export them first)!
- You can also view the outputs in the AWS Console or in using the AWS CLI
- They’re very **useful** for example if you define a **network CloudFormation**, and output the variables such as **VPC ID** and your **Subnet IDs**
- It’s the best way to perform some collaboration cross stack, as you let expert handle their own part of the stack
- You **can’t delete** a CloudFormation Stack if its **outputs are being referenced by another** CloudFormation stack
- one stack uses **`!ImportValue`** to refer to the values outputed by another stack

## 2.5 Conditions
```yaml
UseProdCondition:
  !Equals [!Ref EnvironmentType, prod]
```
```yaml
Conditions: 
  CreateNewSecurityGroup: !Equals [!Ref ExistingSecurityGroup, NONE]

Resources: 
  NewSecurityGroup: 
    Type: "AWS::EC2::SecurityGroup"
    Condition: CreateNewSecurityGroup
```
- Conditions are used to control the **creation of resources** or **outputs based on a condition**
- Conditions can be whatever you want them to be, but common ones are:
  - Environment (dev / test / prod)
  - AWS Region
  - Any parameter value
- Each condition can reference another condition, parameter value or mapping
- The logical ID is for you to choose. It’s how you name condition
- The intrinsic function (logical) can be any of the following:
  - Fn::And
  - Fn::Equals
  - Fn::If
  - Fn::Not
  - Fn::Or
- Conditions can be applied to resources / outputs / etc…
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
