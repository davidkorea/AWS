# AWS
**You have some very sensitive data stored on AWS S3 and want to try every possible alternative to keeping it secure in regards to access control. What are the mechanisms available for access control on AWS S3?**
- a. (IAM) policies, Access Control Lists (ACLs), bucket policies, and query string authentication.
- b. (IAM) policies, Access Control Lists (ACLs) and bucket policies.
- c. Access Control Lists (ACLs), bucket policies, and query string authentication
- d. (IAM) policies, Access Control Lists (ACLs), bucket policies, query string authentication and encryption.

解析： Amazon S3 supports several mechanisms that give you flexibility to control who can access your data as well as how, when, and where they can access it. Amazon S3 provides four different access control mechanisms: AWS Identity and Access Management (IAM) policies, Access Control Lists (ACLs), bucket policies, and query string authentication. IAM enables organizations to create and manage multiple users under a single AWS account. With IAM policies, you can grant IAM users fine-grained control to your Amazon S3 bucket or objects. You can use ACLs to selectively add (grant) certain permissions on individual objects. Amazon S3 bucket policies can be used to add or deny permissions across some or all of the objects within a single bucket. With Query string authentication, you have the ability to share Amazon S3 objects through URLs that are valid for a specified period of time.

> Answer: [a], d X


-----

**You have just discovered that you can upload your objects to Amazon S3 using Multipart Upload API. You start to test it out but are unsure of the benefits that it would provide. Which of the following is not a benefit of using multipart uploads?**
- a. You can begin an upload before you know the final object size.
- b. Quick recovery from any network issues.
- c. Pause and resume object uploads
- d. It's more secure than normal upload. 

解析： Multipart upload in Amazon S3 allows you to upload a single object as a set of parts. Each part is a contiguous portion of the object's data. You can upload these object parts independently and in any order. If transmission of any part fails, you can re-transmit that part without affecting other parts. After all parts of  our object are uploaded, Amazon S3 assembles these parts and creates the object. In general, when your object size reaches 100 MB, you should consider using multipart uploads instead of uploading the object in a single operation. Using multipart upload provides the following advantages:
- Improved throughput—You can upload parts in parallel to improve throughput.
- Quick recovery from any network issues—Smaller part size minimizes the impact of restarting a failed upload due to a network error.
- Pause and resume object uploads—You can upload object parts over time. Once you initiate a multipart upload there is no expiry; you must explicitly complete or abort the multipart upload.
- Begin an upload before you know the final object size—You can upload an object as you are creating it.

> Answer: [d]


-----

??????

**Do you need to shutdown your EC2 instance when you create a snapshot of EBS volumes that serve as root devices?**
 No, you only need to shutdown an instance before deleting it.
 Yes 
 No, the snapshot would turn off your instance automatically.
 No[o]
解析： Yes, to create a snapshot for Amazon EBS volumes that serve as root devices, you should stop the instance before taking the snapshot. Reference: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html

-----

**Select the correct statement: Within Amazon EC2, when using Linux instances, the device name /dev/sda1 is _____.**
- a. reserved for EBS volumes
- b. recommended for EBS volumes
- c. recommended for instance store volumes
- d. reserved for the root device 

解析： Within Amazon EC2, when using a Linux instance, the device name /dev/sda1 is reserved for the root device. Reference: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/device_naming.html

> Answer: [d]

-----

**Which of the following strategies can be used to control access to your Amazon EC2 instances?**
- a. DB security groups
- b. IAM policies
- c. None of these
- d. EC2 security groups 

解析： IAM policies allow you to specify what actions your IAM users are allowed to perform against your EC2 Instances. However, when it comes to access control, security groups are what you need in order to define and control the way you want your instances to be accessed, and whether or not certain kind of communications are allowed or not. Reference: http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/UsingIAM.html

> Answer: [d]

-----

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







