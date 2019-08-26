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
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
