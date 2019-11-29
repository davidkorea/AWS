- subnet1 - Public
- subnet2 - Private
- subnet3 - VPN-only

![](http://tva1.sinaimg.cn/large/006gDTsUgy1g9evv9ojxjj30l80pcgpk.jpg)



-----

- secondary CIDR
    ![image](http://tva3.sinaimg.cn/large/006gDTsUgy1g9a3k8tu6mj30ud06a0sx.jpg)
    ![](https://docs.aws.amazon.com/zh_cn/vpc/latest/userguide/images/vpc-multiple-cidrs.png)
    - Usage: [我如何修改 VPC 上的 CIDR 块来容纳更多主机？](https://aws.amazon.com/cn/premiumsupport/knowledge-center/vpc-modify-cidr-more-hosts/)       
    
-----

**Dedicate tenancy** revert to **default tenancy**:
- Once a VPC is set to Dedicated hosting, it can be changed back to default hosting via the **CLI, SDK or API**
- Note that this will **NOT change** hosting settings for **existing instances**, **ONLY future ones**
- **Existing instances** can be **changed** via **CLI, SDK or API** but need to be **in a stopped state** to do so

VPN:
- a private subnet
- Hardware VPN Access
- VPG
- on-premise Customer Gateway.


**VPC Flow Log** can be created at the following levels:
- VPC level
- Subnet level
- Network Interface level

# 1. Egress Only Internet Gateway
仅出口 Internet 网关
- Egress only Internet Gateway is for IPv6 only
- **Similar** function as a **NAT**, but a NAT is for IPv4
- Good to know: IPv6 are all public addresses
- Therefore all our instances with IPv6 are publicly accessibly
- Egress Only Internet Gateway gives our **IPv6 instances access to the internet**, but they **won’t be directly reachable by the internet**
- After creating an Egress Only Internet Gateway, edit the route tables



# 2. Direct Connect (Different from VPN)
![](https://i.loli.net/2019/08/12/6wRdWbHNUYZTLqJ.png)

If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a Direct Connect Gateway
- Each VPC has a VPG(VPC menu site-to-site VPN, noe can be created in Direct Connect menu)
    `![](https://i.loli.net/2019/08/12/cgWnVaHTKGJ4x57.png)
- Direct Connect Gateway for one AWS account(Direct Connect menu)
    ![](https://i.loli.net/2019/08/12/6Wp75hQaxz2Eymr.png)  

# 3. VPN
> You work for a famous bakery that is deploying a hybrid cloud approach. Their legacy IBM AS400 servers will remain on-premise within their own datacenter. However, they will need to be able to communicate to the AWS environment over a site-to-site VPN connection. What do you need to do to establish the VPN connection?
> - set and **ASN for Vitual Private Gateway**

1. CG
    ![](https://i.postimg.cc/0rgt7cBZ/image.png)

2. VPG
    ![](https://i.postimg.cc/T3BPgmd1/image.png)

3. Site-to-site VPN
    ![](https://i.postimg.cc/4dDM1fKz/image.png)
    
# 4. VPC
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
