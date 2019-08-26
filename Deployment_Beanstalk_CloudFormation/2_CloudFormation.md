- **Templates** have to be **uploaded in S3** and then referenced in CloudFormation
- To **update a template**, we **can’t edit** previous ones. We have to **reupload a new version** of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation.


# Benefits of AWS CloudFormation
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
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
