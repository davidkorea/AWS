- private Git repositories
- No size limit on repositories (scale seamlessly)
- Fully managed, highly available
- Code only in AWS Cloud account => increased security and compliance
- Secure (encrypted, access control, etc…)
- Integrated with Jenkins / CodeBuild / other CI tools

# 1. CodeCommit Security
- Interactions are done using Git (standard)
- Authentication in Git:
  - SSH Keys: **AWS User**s can configure **SSH keys** in their IAM Console
  - HTTPS: Done through the AWS CLI Authentication helper or Generating HTTPS credentials
  - MFA (multi factor authentication) can be enabled for extra safety
- Authorization in Git:
  - IAM Policies manage user / roles rights to repositories
- Encryption:
  - Repositories are automatically encrypted at rest using KMS
  - Encrypted in transit (can only use HTTPS or SSH – both secure)
- Cross Account access:
  - Do not share your SSH keys
  - Do not share your AWS credentials
  - Use IAM Role in your AWS Account and use AWS STS (with AssumeRole API)
  
# 2. CodeCommit Notifications
- You can trigger notifications in CodeCommit using AWS SNS or AWS Lambda or AWS CloudWatch Event Rules
- Use cases for notifications SNS / AWS Lambda notifications:
  - Deletion of branches
  - Trigger for pushes that happens in master branch
  - Notify external Build System
  - Trigger AWS Lambda function to perform codebase analysis (maybe credentials got committed in the code?)
- Use cases for CloudWatch Event Rules:
  - Trigger for pull request updates (created / updated / deleted / commented)
  - Commit comment events
  - CloudWatch Event Rules goes into an SNS topic
