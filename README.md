# AWS

- Direct Connect, **Different from VPN**
    - If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a Direct Connect Gateway
        - Each VPC has a VPG(VPC menu site-to-site VPN, noe can be created in Direct Connect menu)
            `![](https://i.loli.net/2019/08/12/cgWnVaHTKGJ4x57.png)
        - Direct Connect Gateway for one AWS account(Direct Connect menu)
            ![](https://i.loli.net/2019/08/12/6Wp75hQaxz2Eymr.png)  

![](https://i.loli.net/2019/08/12/6wRdWbHNUYZTLqJ.png)

-----

- peering
    - 可以其他账户，可以其他region
    - 每一个VPC子网都需要设置route table，将对方的CIDR路由到peering connection

- endpoint
    - 需要指定IAM role和`--region`
    ```
    [ec2-user@ip-10-0-12-115 ~]$ aws s3 ls          # NO IAM role
    Unable to locate credentials. You can configure credentials by running "aws configure".
    
    [ec2-user@ip-10-0-12-115 ~]$ aws s3 ls          # default region is us-east-1
    ^C

    [ec2-user@ip-10-0-12-115 ~]$ aws s3 ls --region ap-northeast-2      # seoul region
    2019-07-21 03:12:51 www.davidkorea.com
    ```


![](https://i.loli.net/2019/08/11/aSDYNsk9RM1gWeE.png)


-----

- **stop， restart EC2 instance will not lose Private IPv4.**
    ![](https://i.loli.net/2019/08/10/fDc3owzHSvWJByP.png)
    
-----



```
AWS Certified Cloud Practitioner - Practice (Chinese Mandarin Simplified) 

......您好：

感谢您参加AWS Certified Cloud Practitioner - Practice 考试。请检查以下信息，以确定可能需要额外准备哪些主题。

总得分： 100%

主题平均得分：
1.0  Cloud Concepts: 100%
2.0  Security: 100%
3.0  Technology: 100%
4.0  Billing and Pricing: 100%

考试指南

点击此处 获得有关 AWS Certified Cloud Practitioner 考试的更多信息。将有一个用于从该页面下载 AWS Certified Cloud Practitioner 考试指南的链接。这些链接所提供的信息可帮助您准备认证考试。

请勿回复此电子邮件，如果您对 AWS 认证计划有任何问题，请联系我们，以获得帮助。

谢谢。 
```




-----

LVS-NAT based on EC2 amazon linux

-----

siyin training - finished

![]()
-----

내일 출발 

야딩 ticket

-----

20190630 1차 BD 

-----





-----

django + wxapp + aws beanstalk

-----

190603 지원했음 

-----

spring cloud

-----

lvs

springcloud

-----


1. fighting!!!!!!ㅋㅠ    
-----

1. hadoop MapReduce hive spark
-----

1. redis

-----

1. AWS Lamda + AWS batch + AWS BeansTalk
    - lambda spider
2. blog
    - s3 website + cloudfront + Certifacate manager + route53

-----

1. docker networking
2. interview prapare
    - 경력직 면접 
    - recuiter view
3. resume modify


