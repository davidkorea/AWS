![](https://i.bmp.ovh/imgs/2019/08/253c67e19ad75502.png)

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
