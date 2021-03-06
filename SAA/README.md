# AWS

allocate 划分，分配

finer grain 更细的颗粒度，更详细的





-----

![](https://i.loli.net/2019/08/02/5d43e3a39492c15908.jpg)

**参考： [向 Auto Scaling 组添加 Elastic Load Balancing 运行状况检查](https://docs.aws.amazon.com/zh_cn/autoscaling/ec2/userguide/as-add-elb-healthcheck.html)**

- The **default health checks** for an **Auto Scaling group** are **EC2 status checks only**. If an instance fails these status checks, the **Auto Scaling group** considers the instance unhealthy and replaces it. 
  - ASG 默认针对单个EC2进行健康检查，检查失败，则替换该实例，并创建新的

- If you attached one or more **load balancers or target groups** to your **Auto Scaling group**, the group does not, by default, consider an instance unhealthy and replace it if it fails the load balancer health checks. 
  - 如果是将 负载均衡/目标组关联到ASG，还是设置为按照默认对单个EC2进行健康检查。如果负载均衡报告实例健康检查失败，那ASG也不做任何处理。
  - 因为默认ASG只检查EC2实例，而当前ASG挂载了负载均衡器，而负载均衡并不算是实例（也可能可以把负载均衡当做EC2，由于负载均衡器是健康的，其中至少一个健康实例）。所以，ASG不会剔除由ALB检查出来的不健康实例

- However, you can optionally configure the **Auto Scaling grou**p to **use Elastic Load Balancing health checks**.This ensures that the group can determine an instance's health based on additional **tests provided by the load balancer**. The load balancer periodically sends pings,attempts connections, or sends requests to test the EC2 instances. These tests arecalled health checks. 
  - 将默认检查EC2，改为检查ELB的健康。那么ASG可以接受ELB的健康检查结果

![](https://i.loli.net/2019/08/02/5d43e6f5450e127546.png)
![](https://i.loli.net/2019/08/02/5d43e6f791f2153448.png)

**参考： [Wordpress最佳实践 - Auto Scaling Group](https://github.com/davidkorea/AWS/blob/master/SAA/30_HA_Wordpress.md#34-auto-scaling-group)**
![](https://camo.githubusercontent.com/2e313a08b65db6871cb39b3ae40c1c15a609cb6b/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30372f31312f3564323661663634383434623731393330372e706e67)

-----

New AWS account soft limit of 20 EC2 per region?? per AZ?? -- **per region**

-----

**Can I "force" a failover for any RDS instance that has Multi-AZ configured? -- YES**

-----

There is a requirement to get the IP addresses for resources accessed in a private subnet.
Which of the following can be used to fulfill this purpose?
 Trusted Advisor
 VPC Flow Logs
 Use CloudWatch metrics
 Use CloudTrail
 
 -----
 
A company is developing a web application to be hosted in AWS.
This application needs a data store for session data. As an AWS Solution Architect, which of the following would you recommend as an ideal option to store session data? Choose 2 answers from the options given below.
 CloudWatch
 DynamoDB
 Elastic Load Balancing
 ElastiCache
 Storage Gateway
 
 -----
 
A Solutions Architect is designing a shared service for hosting containers from several customers on Amazon ECS.
These containers will use several AWS services. A container from one customer should not be able access data from another customer.
Which of the below solutions should the architect use to meet these requirements?
 IAM roles for tasks
 IAM roles for EC2 Instances
 IAM Instance profile for EC2 Instances
 Security Group rules
 
-----

An organization hosts a multi-language website on AWS, which is served using CloudFront.
Language is specified in the HTTP request as shown below:
http://d11111f8.cloudfront.net/main.html?language=de
http://d11111f8.cloudfront.net/main.html?language=en
http://d11111f8.cloudfront.net/main.html?language=es
How should AWS CloudFront be configured to delivered cache data in the correct language?
 Forward cookies to the origin
 Based on query string parameters
 Cache objects at the origin
 Serve dynamic content
 
-----

A Solutions Architect designing a solution to store and archive corporate documents, has determined Amazon Glacier as the right choice of solution.
An important requirement is that the data must be delivered within 10 minutes of a retrieval request. Which feature in Amazon Glacier can help meet this requirement?
 Vault Lock
 Expedited retrieval
 Bulk retrieval
 Standard retrieval






























-----

**When creating an RDS instance you can select which availability zone in which to deploy your instance.**
- True

**You can select a specific Availability Zone in which to place your DynamoDB Table**
- False
  ![](https://i.loli.net/2019/06/24/5d108934862a262540.png)

-----

**Amazon RDS does not currently support increasing storage on a ____ Db instance**
- SQL Server

-----

**Can I delete a snapshot of an EBS Volume that is used as the root device of a registered AMI?**
- NO

-----

**Is it possible to perform actions on an existing Amazon EBS Snapshot?**
- yes，cli, console, apis. create ami, copy, add tag, modify permission


-----

- 可以对整个存储桶设置 默认加密属性。已上传对未加密文件，可以被自动加密吗？？？？？

-----

**You manage a high performance site that collects scientific data using a bespoke protocol over TCP port 1414. The data comes in at high speed and is distributed to an autoscaling group of EC2 compute services spread over three AZs. Which type of AWS Load Balancer would best meet this requirement.** NLB

the Network Load Balancer is specifically designed for high performance traffic that is not conventional Web traffic. The Classic LB might also do the job, but would not offer the same performance.


-----

**You have a web site with three distinct services each hosted by different web server autoscaling groups. Which AWS service should you use.** ALB


The ALB has functionality to distinguish traffic for different targets (mysite.co/accounts vs. mysite.co/sales vs. mysite.co/support) and distribute traffic based on rules for; target group, condition, and priority.

-----

**Until recently customers were not permitted to conduct Penetration Testing without AWS engagement. However that has changed. There are still conditions however.** yes

In a custom VPC with new subnets in each AZ, there is a Route that supports communication across all subnets/AZs. Plus a Default SG with an allow rule 'All traffic, All protocols, All ports, from anything using this Default SG'.

-----

**Are you permitted to conduct your own vulnerability scans on your own VPC without alerting AWS first?** yes

Until recently customers were not permitted to conduct Penetration Testing without AWS engagement. However that has changed. There are still conditions however.

-----

**Your company hosts 10 web servers all serving the same web content in AWS. They want Route 53 to serve traffic to random web servers. Which routing policy will meet this requirement, and provide the best resiliency?** Multivalue ROuting.

Multivalue answer routing lets you configure Amazon Route 53 to return multiple values, such as IP addresses for your web servers, in response to DNS queries. Route 53 responds to DNS queries with up to eight healthy records and gives different answers to different DNS resolvers. The choice of which to use is left to the requesting service effectively creating a form or randomisation.


-----

**You have created a new subdomain for your popular website, and you need this subdomain to point to an Elastic Load Balancer using Route53. Which DNS record set should you create?**
- A
- **CNAME**

> ### top domain use A? subdomain use CNAME??????















-----

-----

-----

**A user has deployed an application on his private cloud. The user is using his own monitoring tool. He wants to configure it so that whenever there is an error, the monitoring tool will notify him via SMS. Which of the below mentioned AWS services will help in this scenario?**
- a. AWS SES
- b. AWS SNS 
- c. None because the user infrastructure is in the private cloud
- d. AWS SMS

解析： Amazon Simple Notification Service (Amazon SNS) is a fast, flexible, and fully managed push messaging service. Amazon SNS can be used to make push notifications to mobile devices. Amazon SNS can deliver notifications by SMS text message or email to the Amazon Simple Queue Service (SQS) queues or to any HTTP endpoint. In this case user can use the SNS apis to send SMS. Reference: http://aws.amazon.com/sns/

> Answer: [b], c X

-----

**You have just set up a large site for a client which involved a huge database which you set up with Amazon RDS to run as a Multi-AZ deployment. You now start to worry about what will happen if the database instance fails. Which statement best describes how this database will function if there is a database failure?**
- a. Updates to your DB Instance are synchronously replicated across Availability Zones to the standby in order to keep both in sync and protect your latest database updates against DB Instance failure. 
- b. Your database will not resume operation without manual administrative intervention.
- c. Updates to your DB Instance are asynchronously replicated across Availability Zones to the standby in order to keep both in sync and protect your latest database updates against DB Instance failure.
- d. Updates to your DB Instance are synchronously replicated across S3 to the standby in order to keep both in sync and protect your latest database updates against DB Instance failure.

解析：
- Amazon Relational Database Service (Amazon RDS) is a managed service that makes it easy to set up, operate, and scale a relational database in the cloud. It provides cost-efficient and resizable capacity, while managing time-consuming database administration tasks, freeing you up to focus on your applications and business.
- When you create or modify your DB Instance to run as a Multi-AZ deployment, Amazon RDS automatically provisions and maintains a synchronous “standby” replica in a different Availability Zone.
- Updates to your DB Instance are synchronously replicated across Availability Zones to the standby in order to keep both in sync and protect your latest database updates against DB Instance failure. During certain types of planned maintenance, or in the unlikely event of DB Instance failure or Availability Zone failure, Amazon RDS will automatically failover to the standby so that you can resume database writes and reads as soon as the standby is promoted. Since the name record for your DB Instance remains the same, you application can resume database operation without the need for manual administrative intervention.
- With Multi-AZ deployments, replication is transparent: you do not interact directly with the standby, and it cannot be used to serve read traffic. If you are using Amazon RDS for MySQL and are looking to scale read traffic beyond the capacity constraints of a single DB Instance, you can deploy one or more Read Replicas.

> Answer: [a]

-----

**Which of the following statements are rue about Amazon Route 53 resource records? Choose 2 answers**

- a. An Alias record can map one DNS name to another Amazon Route 53 DNS name.  
- b. A CNAME record can be created for your zone apex.
- c. An Amazon Route 53 CNAME record can point to any DNS record hosted anywhere. 
- d. TTL can be set for an Alias record in Amazon Route 53.
- e. An Amazon Route 53 Alias record can point to any DNS record hosted anywhere.

> Answer: [a], [c], d X

-----

**You are designing Internet connectivity for your VPC. The Web servers must be available on the Internet. The application must have a highly available architecture. Which alternatives should you consider? (Choose 2 answers)**
 

- a. Configure a NAT instance in your VPC. Create a default route via the NAT instance and associate it with all subnets. Configure a DNS A record that points to the NAT instance public IP address.
- b. Configure a CloudFront distribution and configure the origin to point to the private IP addresses of your Web servers. Configure a Route53 CNAME record to your CloudFront distribution.
- c. Place all your web servers behind ELB. Configure a Route53 CNAME to point to the ELB DNS name. 
- d. Assign EIPs to all web servers. Configure a Route53 record set with all EIPs with health checks and DNS failover. 
- e. Configure ELB with an EIP. Place all your Web servers behind ELB. Configure a Route53 A record that points to the EIP.

> Answer: [c], [d], b X

-----

**You have deployed a web application targeting a global audience across multiple AWS Regions under the domain name.example.com. You decide to use Route53 Latency-Based Routing to serve web requests to users from the region closest to the user. To provide business continuity in the event of server downtime you configure weighted record sets associated with two web servers in separate Availability Zones per region. Dunning a DR test you notice that when you disable all web servers in one of the regions Route53 does not automatically direct all users to the other region. What could be happening? {Choose 2 answers)**
 
- a. Latency resource record sets cannot be used in combination with weighted resource record sets.
- b. You did not setup an HTTP health check to one or more of the weighted resource record sets associated with the disabled web servers. 
- c. The value of the weight associated with the latency alias resource record set in the region with the disabled servers is higher than the weight for the other region.
- d. One of the two working web servers in the other region did not pass its HTTP health check.
- e. You did not set "Evaluate Target Health" to "Yes" on the latency alias resource record set associated with example com in the region where you disabled the servers. 
 
解析： How Health Checks Work in Complex Amazon Route 53 Configurations Checking the health of resources in complex configurations works much the same way as in simple configurations. However, in complex configurations, you use a combination of alias resource recordsets (including weighted alias, latency alias, and failover alias) and nonalias resource record sets to build a decision tree that gives you greater control over how Amazon Route 53 responds to requests. For more information, see How Health Checks Work in Simple Amazon Route 53 Configurations.

> Answer: [b], [e]

-----

**Having set up a website to automatically be redirected to a backup website if it fails, you realize that there are different types of failovers that are possible. You need all your resources to be available the majority of the time. Using Amazon Route 53 which configuration would best suit this requirement?****
- a. Active-active failover. 
- b. None. Route 53 can't failover.
- c. Active-passive failover.
- d. Active-active-passive and other mixed configurations

解析： You can set up a variety of failover configurations using Amazon Route 53 alias: weighted, latency, geolocation routing, and failover resource record sets.
Active-active failover: Use this failover configuration when you want all of your resources to be available the majority of the time. When a resource becomes unavailable, Amazon Route 53 can detect that it's unhealthy and stop including it when responding to queries.
Active-passive failover: Use this failover configuration when you want a primary group of resources to be available the majority of the time and you want a secondary group of resources to be on standby in case all of the primary resources become unavailable. When responding to queries, Amazon Route 53 includes only the healthy primary resources. If all of the primary resources are unhealthy, Amazon Route 53 begins to include only the healthy secondary resources in response to DNS queries.
Active-active-passive and other mixed configurations: You can combine alias and non-alias resource record sets to produce a variety of Amazon Route 53 behaviors.

> Answer: [a], c X

-----

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
- No, you only need to shutdown an instance before deleting it.
- Yes 
- No, the snapshot would turn off your instance automatically.
- No

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







