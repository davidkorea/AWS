- We want to deploy our application automatically to many **EC2 instances** that are **NOT** managed by Elastic **Beanstalk**
- There are several ways to handle deployments using open source tools (Ansible, Terraform, Chef, Puppet, etc…)
- **EC2 instances** are grouped by **deployment group** (dev / test / prod)
- Lots of flexibility to define any kind of deployments
- CodeDeploy can be **chained into CodePipeline** and use artifacts from there
- CodeDeploy can re-use existing setup tools, works with any application, auto scaling integration
- **Blue / Green only works with EC2 instances (not on premise)**
- Support for AWS Lambda deployments (we’ll see this later)
- CodeDeploy **does not provision resources**，不用于创建资源

# 1. CodeDeploy – Steps to make it work
- Each EC2 Machine (or On Premise machine) must be running the **CodeDeploy Agent**
- The agent is continuously polling AWS CodeDeploy for work to do
- CodeDeploy sends **`appspec.yml`** file.
- Application is pulled from GitHub or S3
- EC2 will run the deployment instructions
- CodeDeploy Agent will report of success / failure of deployment on the instance

# 2. CodeDeploy Primary Components
- **Application**: unique name
- **Compute platform**: EC2/On-Premise or Lambda
- **Deployment configuration**: Deployment rules for success / failures
  - EC2/On-Premise: you can specify the minimum number of healthy instances for the deployment.
  - AWS Lambda: specify how traffic is routed to your updated Lambda function versions.
- **Deployment group**: group of tagged instances (allows to deploy gradually)
- **Deployment type**: In-place deployment or Blue/green deployment:
- **IAM instance profile**: need to give EC2 the permissions to pull from S3 / GitHub
- **Application Revision**: application code + appspec.yml file
- **Service role**: Role for CodeDeploy to perform what it needs
- **Target revision**: Target deployment application version
