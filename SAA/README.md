# AWS


**Which of the following statements is true of creating a launch configuration using an EC2 instance?**
- a. The launch configuration can be created only using the Query APIs.
- b. Auto Scaling automatically creates a launch configuration directly from an EC2 instance. 
- c. A user should manually create a launch configuration before creating an Auto Scaling group.
- d. The launch configuration should be created manually from the AWS CLI.

解析： You can create an Auto Scaling group directly from an EC2 instance. When you use this feature, Auto Scaling automatically creates a launch configuration for you as well. Reference: http://docs.aws.amazon.com/AutoScaling/latest/DeveloperGuide/create-lc-with-instanceID.html

> Answer: [b], c X

-----

- IAM user has adminfullaccess, set in cli configuration in EC2
- EC2 has a IAM Role has only S3fullaccess
  - should cli can access other resources except S3, or S3 only?????


-----

**Your system recently experienced down time during the troubleshooting process. You found that a new administrator mistakenly terminated several production EC2 instances. Which of the following strategies will help prevent a similar situation in the future? The administrator still must be able to: launch, start stop, and terminate development resources & launch and start production instances.**

- a. Create an lAM user, which is not allowed to terminate instances by leveraging production EC2 termination protection.
- b. Leverage resource based tagging along with an lAM user, which can prevent specific users from terminating production EC2 resources. 
- c. Leverage EC2 termination protection and multi-factor authentication, which together require users to authenticate before terminating EC2 instances
- d. Create an lAM user and apply an lAM role which prevents users from terminating production EC2 instances.

> Answer: [b]







