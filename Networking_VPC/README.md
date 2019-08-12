Networking -VPC

# 1. Egress Only Internet Gateway
仅出口 Internet 网关
- Egress only Internet Gateway is for IPv6 only
- Similar function as a NAT, but a NAT is for IPv4
- Good to know: IPv6 are all public addresses
- Therefore all our instances with IPv6 are publicly accessibly
- Egress Only Internet Gateway gives our IPv6 instances access to the internet, but they won’t be directly reachable by the internet
- After creating an Egress Only Internet Gateway, edit the route tables



# 2. Direct Connect (Different from VPN)
![](https://i.loli.net/2019/08/12/6wRdWbHNUYZTLqJ.png)

If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a Direct Connect Gateway
- Each VPC has a VPG(VPC menu site-to-site VPN, noe can be created in Direct Connect menu)
    `![](https://i.loli.net/2019/08/12/cgWnVaHTKGJ4x57.png)
- Direct Connect Gateway for one AWS account(Direct Connect menu)
    ![](https://i.loli.net/2019/08/12/6Wp75hQaxz2Eymr.png)  


# 3. vpc
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
