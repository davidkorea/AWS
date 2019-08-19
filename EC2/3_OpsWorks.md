# 1. OpsWorks
- AWS OpsWorks provides three solutions to configure your infrastructure
  - OpsWorks Stacks
    - Define, group, provision, deploy, and operate your applications in AWS by using Chef in local mode.

  - OpsWorks for **Chef** Automate
    - Create Chef servers that include Chef Automate premium features, and use the Chef DK or any Chef tooling to manage them.

  - OpsWorks for **Puppet** Enterprise
    - Create Puppet servers that include Puppet Enterprise features. Inspect, deliver, update, monitor, and secure your infrastructure.

- They work great with **EC2 & On Premise** VM
- AWS Opsworks = Managed Chef & Puppet
- It’s an alternative to AWS SSM

# 2. Quick word on Chef / Puppet

- They help with managing configuration as code
- Helps in having consistent deployments
- Works with Linux / Windows
- Can automate: user accounts, cron, ntp, packages, services…
- They leverage “Recipes” or ”Manifests”
- Chef / Puppet have similarities with SSM / Beanstalk / CloudFormation
but they’re open-source tools that work cross-cloud
