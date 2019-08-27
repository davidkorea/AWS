- Continuous delivery
- Visual workflow
- Source: GitHub / CodeCommit / Amazon S3
- Build: CodeBuild / Jenkins / etc…
- Load Testing: 3rd party tools
- Deploy: AWS CodeDeploy / Beanstalk / CloudFormation / ECS…
- Made of stages:
  - Each stage can have sequential actions and / or parallel actions
  - Stages examples: Build / Test / Deploy / Load Test / etc…
  - Manual approval can be defined at any stage
  
# 1. AWS CodePipeline Artifacts
- Each pipeline stage can create ”artifacts”
- Artifacts are passed stored in Amazon S3 and passed on to the next stage

![](https://i.postimg.cc/QNJPgZD5/image.png)
