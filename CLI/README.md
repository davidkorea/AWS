
# AWS CLI

## Install AWS CLI Locally
Installing the AWS CLI **version 2** on macOS  [https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-mac.html#cliv2-mac-install-confirm](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-mac.html#cliv2-mac-install-confirm)

- has no dependencies on other software packages. It has a self-contained, embedded copy of all dependencies included in the installer
- no longer need to install and maintain Python to use the AWS CLI


Confirming the installation

```bash
yong@MacBookPro ~ % aws --version
aws-cli/2.0.5 Python/3.7.4 Darwin/19.4.0 botocore/2.0.0dev9

yong@MacBookPro ~ % which aws
/usr/local/bin/aws
```

AWS CLI Configuration
```bash
yong@MacBookPro ~ % aws configure
AWS Access Key ID [None]: AXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ
AWS Secret Access Key [None]: XXXXXXXXXXXXXXXXXXXXXXXXXXX
Default region name [None]: ap-northeast-2
Default output format [None]: JSON
```

Configuration files will be stored in `~/.aws`
```bash
yong@MacBookPro ~ % ls ~/.aws
config		credentials
```

##  EC2 NEVER EVER use personal credentials
- use **IAM ROLE**

1. create a EC2 role which has S3-READ-ONLY access
2. goto EC2 - aattach this ROLE
3. can run `aaws s3 ls` on your EC2





