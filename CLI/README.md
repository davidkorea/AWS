
# AWS CLI

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

aws CLI configuration
```bash
yong@MacBookPro ~ % aws configure
AWS Access Key ID [None]: AXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ
AWS Secret Access Key [None]: XXXXXXXXXXXXXXXXXXXXXXXXXXX
Default region name [None]: ap-northeast-2
Default output format [None]: JSON
```
