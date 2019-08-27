![](https://i.postimg.cc/FRQF2X7m/image.png)

- AWS CodeCommit: storing our code
- AWS CodePipeline: automating our pipeline from code to ElasticBeanstalk
- AWS CodeBuild: building and testing our code
- AWS CodeDeploy: deploying the code to EC2 fleets, **NOT Beanstalk**

## Continuous Integration
- Developers push the code to a **code repository** often (GitHub / CodeCommit / Bitbucket / etc…)
- A **testing / build server** **checks the code** as soon as it’s pushed (CodeBuild / **Jenkins CI** / etc…)
- The developer gets feedback about the tests and checks that have passed / failed

## Continuous Delivery
- Ensure that the software can be released reliably whenever needed.
- Ensures deployments happen often and are quick
- Shift away from “one release every 3 months” to ”5 releases a day”
- That usually means **automated deployment**
  - CodeDeploy
  - **Jenkins CD**
  - Spinnaker
  - Etc…
