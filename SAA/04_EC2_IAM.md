# 为EC2分配IAM角色






You can authenticate using an MFA device in the following ways:
- Through the AWS Management Console – the user is prompted for a user name, password and authentication code
- Using the AWS API – restrictions are added to IAM policies and developers can request temporary security credentials and pass MFA parameters in their AWS STS API requests
- Using the AWS CLI by obtaining temporary security credentials from STS (aws sts get-session-token)




-----

但使用Access Key ID和AWS Secret Access Key的凭证这种方式是不安全的，如果一旦这个凭证被泄露，任何人都可以通过这个凭证来获取你AWS内的资源。而且，如果你在管理成千上万台有类似需求的EC2实例，那么如果有一天需要更改Access Key ID和AWS Secret Access Key的时候，将是一场噩梦。

这个时候，AWS引进了角色（Role）的概念，可以通过角色的方式来赋予EC2实例一定的权限。并且你可以在IAM (Identity Access Management) 中随时更改角色都具有的权限，马上可以生效。

需要注意的是，在以前，角色是只能在EC2实例创建的时候赋予的，一旦赋予不能改变；而在近年的更新中，角色已经可以在EC2运行的时候再赋予或者更改了。

# Add a Role to a running EC2
![](https://i.loli.net/2019/06/14/5d031a51beafd25271.png)

```sh
[ec2-user@ip-172-31-26-251 ~]$ aws s3 ls
Unable to locate credentials. You can configure credentials by running "aws configure".
[ec2-user@ip-172-31-26-251 ~]$ aws s3 ls
2019-05-24 06:21:17 www.david.com
```
# Exam Tips
![](https://i.loli.net/2019/06/15/5d04ffda5a2e237382.png)
