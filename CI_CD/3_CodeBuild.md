![](https://i.postimg.cc/WbPZvxq9/image.png)

- Alternative to other build tools such as **Jenkins**
- Continuous scaling (no servers to manage or provision – no build queue)
- Pay for usage: **the time it takes** to complete the builds
- Leverages **Docker under the hood** for reproducible builds
- Possibility to **extend capabilities** leveraging our **own base Docker images**
- Secure: Integration with KMS for encryption of build artifacts, IAM for build permissions, and VPC for network security, CloudTrail for API calls logging

- Source Code from GitHub / CodeCommit / CodePipeline / S3…
- Build **instructions can be** defined in **code (buildspec.yml file)**
- Output logs to Amazon S3 & AWS CloudWatch Logs
- Metrics to monitor CodeBuild statistics
- Use CloudWatch Events to detect failed builds and trigger notifications
- Use CloudWatch Alarms to notify if you need “thresholds” for failures
- CloudWatch Events / AWS Lambda as a Glue
- SNS notifications
- Ability to reproduce CodeBuild locally to troubleshoot in case of errors
- Pipelines can be defined within CodePipeline or CodeBuild itself

- Support environments
  - Java
  - Ruby
  - Python
  - Go
  - Node.js
  - Android
  - .NET Core
  - PHP
  - Docker: extend any environment you like
