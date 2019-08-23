- **Under the hood**在幕后, Elastic Beanstalk **relies on CloudFormation**

# 1. Beanstalk Basics
- Elastic Beanstalk has three components

  - Application
    ![](https://i.postimg.cc/k4gLp3cL/QQ-20190823151530.png)
  - Application version: each deployment gets assigned a version
    ![](https://i.postimg.cc/Vv72jYbK/image.png)
    ![](https://i.postimg.cc/j2srHg3S/image.png)
  - Environment name (dev, test, prod…): free naming
    ![](https://i.postimg.cc/q7fScDzH/image.png)

  - app下，包含app version
  - app下，包含运行环境，如开发，测试，生产。每个环境都有独立的EC2，S3等设施
  - 而每个app版本，都可部署到不同的环境下
    ![](https://i.postimg.cc/zGdM6Dt5/QQ-20190823155822.png)
    
- You deploy application versions to environments and can promote application versions to the next environment
- Rollback feature to previous application version
- Full control over lifecycle of environments


# 2. Deployment Options for Updates
- All at once (deploy all in one go) – fastest, but instances aren’t available to serve traffic for a bit (downtime)
- Rolling: update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy
- Rolling with additional batches: like rolling, but spins up new instances to move the batch (so that the old application is still available)
- Immutable: spins up new instances in a new ASG, deploys version to these instances, and then swaps all the instances when everything is healthy

# 3. Blue / Green Deployment
- Not a “direct feature” of Elastic Beanstalk
- Zero downtime and release facility
- Create a **new “stage” environment** and deploy v2 there
- The new environment (green) can be validated independently and roll back if issues
- Route 53 can be setup using weighted policies to redirect a little bit of traffic to the stage environment
- Using Beanstalk, “swap URLs” when done with the environment test

![](https://i.postimg.cc/rp74wby3/QQ-20190823160759.png)

# 4. Elastic Beanstalk Extensions
- A **zip file** containing our code must be deployed to Elastic Beanstalk
- All the parameters set in the UI can be configured with code using files
- Requirements:
  - in the **`.ebextensions/`** directory in the root of source code
  - YAML / JSON format
  - `.config` extensions (example: logging.config)
  - Able to modify some default settings using: option_settings
  - Ability to add resources such as RDS, ElastiCache, DynamoDB, etc…
- Resources managed by .ebextensions get deleted if the environment goes away

# 5. Deployment Mechanism
- Describe dependencies
  - **`requirements.txt`** for Python
  - **`package.json`** for Node.js
- Package code as zip
- Zip file is uploaded to each EC2 machine
- Each EC2 machine resolves dependencies (SLOW)
- Optimization in case of long deployments: Package dependencies with source code to improve deployment performance and speed

# 6. Beanstalk with HTTPS
- Idea: Load the SSL certificate onto the Load Balancer
  - Can be done from the Console (EB console, load balancer configuration)
    ![]()
  - Can be done from the code: **`.ebextensions/securelistener-alb.config`**
- SSL Certificate can be provisioned using ACM (AWS Certificate Manager) or CLI
- Must configure a security group rule to allow incoming port 443 (HTTPS port)

Beanstalk redirect HTTP to HTTPS
- Configure your instances to redirect HTTP to HTTPS: https://github.com/awsdocs/elastic-beanstalk-samples/tree/master/configuration-files/awsprovided/security-configuration/https-redirect
- OR configure the Application Load Balancer (ALB only) with a rule
- Make sure health checks are not redirected (so they keep giving 200 OK)

# 7. Beanstalk Lifecycle Policy
- Elastic Beanstalk can store at most 1000 application versions
- If you don’t remove old versions, you won’t be able to deploy anymore
- To phase out old application versions, use a lifecycle policy
  - Based on time (old versions are removed)
  - Based on space (when you have too many versions)
- Versions that are currently used won’t be deleted
- Option not to delete the source bundle in S3 to prevent data loss

![](https://i.postimg.cc/1t8Rd5pN/QQ-20190823162537.png)

# 8. Web Server vs Worker Environment
- If your application performs tasks that are **long to complete**, offload these tasks to a dedicated worker environment
- Decoupling your application into two tiers is common
- Example: processing a video, generating a zip file, etc
- You can define periodic tasks in a file cron.yaml

![](https://i.postimg.cc/D0cNd18Q/image.png)

# 9. RDS with Elastic Beanstalk
- RDS can be provisioned with Beanstalk, which is great for dev / test
  - This is not great for prod as the database lifecycle is tied to the Beanstalk environment lifecycle
  - The best for prod is to separately create an RDS database and provide our EB application with the connection string
- Steps to migrate from RDS coupled in EB to standalone RDS:
  - Take an RDS DB snapshot
  - Enable deletion protection in RDS
  - Create a new environment without an RDS, point to existing old RDS
  - Perform blue/green deployment and swap new and old environments
  - Terminate the old environment (RDS won’t get deleted thanks to protection)
  - Delete CloudFormation stack (will be in DELETE_FAILED state)




















