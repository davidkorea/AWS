# JupyterHub with Kubernetes on EC2

1. Setup a Kubernetes cluster on AWS
2. Setup JupyterHub
    - Setting up Helm
    - Setting up JupyterHub
    - Tearing Everything Down

# 0. Prerequisites 
1. Redhat EC2 
2. python，pip
3. awscli

# 1. Kubernetes cluster on AWS
## 1.1 kubectl
https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/install-kubectl.html#install-kubectl-linux

- ```curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.7/2019-03-27/bin/linux/amd64/kubectl```
- ```chmod +x ./kubectl```
- ```mkdir $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH```
- ```kubectl version --short --client```
## 1.2 kops
https://kubernetes.io/docs/setup/custom-cloud/kops/

- ```wget https://github.com/kubernetes/kops/releases/download/1.10.0/kops-linux-amd64```
- ```chmod +x kops-linux-amd64```
- ```mv kops-linux-amd64 /usr/local/bin/kops```
