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





































