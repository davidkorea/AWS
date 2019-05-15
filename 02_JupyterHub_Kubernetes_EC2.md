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
https://kubernetes.io/docs/setup/custom-cloud/kops/ , https://github.com/kubernetes/kops#installing

We like to think of it as kubectl for clusters.

kops helps you create, destroy, upgrade and maintain production-grade, highly available, Kubernetes clusters from the command line. AWS (Amazon Web Services) is currently officially supported, with GCE in beta support , and VMware vSphere in alpha, and other platforms planned.

> release version
> - https://api.github.com/repos/kubernetes/kops/releases/latest
> - find "tag_name": "1.11.1"
> - ```curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64```
> - 使用1.11.1版本，否则后面helm安装时会报错
> Error: Chart requires kubernetesVersion: >=1.11.0-0 which is incompatible with Kubernetes v1.10.13

- ~~```wget https://github.com/kubernetes/kops/releases/download/1.10.0/kops-linux-amd64```~~
- ```wget https://github.com/kubernetes/kops/releases/download/1.11.1/kops-linux-amd64```
- ```chmod +x kops-linux-amd64```
- ```mv kops-linux-amd64 /usr/local/bin/kops```
- ```kops version```

## 1.3 AWS IAM account
- create on aws, and download secret key as txt
    ![](https://i.loli.net/2019/05/15/5cdb846397d1647207.png)

- ```aws configure```, as created above
    ```
    [root@server ~]# aws configure
    AWS Access Key ID [None]: aaaaaaaaaaaaaaaaaaaaaa
    AWS Secret Access Key [None]: bbbbbbbbbbbbbbbbbbbbbbbbb
    Default region name [None]: ap-northeast-2
    Default output format [None]: json
    ```

- 使用kops 署集群，需要为kops创建一个 IAM 用户kops，并分配相应的权限。包括：
    - AmazonEC2FullAccess
    - AmazonRoute53FullAccess
    - AmazonS3FullAccess
    - IAMFullAccess
    - AmazonVPCFullAccess

    ```
    aws iam create-group --group-name kops
    aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name kops
    aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess --group-name kops
    aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --group-name kops
    aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMFullAccess --group-name kops
    aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess --group-name kops
    aws iam create-user --user-name kops
    aws iam add-user-to-group --user-name kops --group-name kops
    ```
    - 创建后，我们可以在 AWS 控制台的 UI 上看到用户
    
 - 为 kops 用户创建密钥, 接着通过 aws configure 更新 awscli的配置，让它使用新创建的 kops 用户的密钥
    - ```aws iam create-access-key --user-name kops```
        ```
        [root@server ~]# aws iam create-access-key --user-name kops
        {
            "AccessKey": {
                "UserName": "kops",
                "Status": "Active",
                "CreateDate": "2019-05-13T07:59:40Z",
                "SecretAccessKey": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
                "AccessKeyId": "bbbbbbbbbbbbbbb"
            }
        }
        ```

