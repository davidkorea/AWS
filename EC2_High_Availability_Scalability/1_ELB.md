# 1. CLB and ALB support SSL certificates and provide SSL termination
- ALB - Linseners - Add HTTPS443 Port
   ![](https://i.loli.net/2019/08/09/eVhOSNCL96AptHr.png)


# 2. CLB and ALB support Stickiness
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances
- The “cookie” used for stickiness has an expiration date you control
   ![](https://i.loli.net/2019/08/09/6sFVTP742DdpOAK.png)
