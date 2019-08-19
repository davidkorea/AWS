

We need to install the SSM agent onto the systems we control
  - Installed by default on Amazon Linux AMI & some Ubuntu AMI
  - If an instance can’t be controlled with SSM, it’s probably an issue with the SSM agent!
Make sure the EC2 instances have a proper IAM role to allow SSM actions
