


# JupyterHub with Kubernetes on EC2
References
- [AWS EC2 上安装 Kubernetes 的示例](https://www.jianshu.com/p/7d448abae039)
- [kubernetes/kops/docs/aws.md](https://github.com/kubernetes/kops/blob/master/docs/aws.md#prepare-local-environment)
- [kubernetes/kops/docs/Installing kops (Binaries)](https://github.com/kubernetes/kops/blob/master/docs/install.md)
- [Setting Up a Kubernetes Cluster on AWS in 5 Minutes](https://ramhiser.com/post/2018-05-20-setting-up-a-kubernetes-cluster-on-aws-in-5-minutes/)
- [JupyterHub with Kubernetes On Single Bare Metal Instance Tutorial](https://medium.com/@georgepaw/jupyterhub-with-kubernetes-on-single-bare-metal-instance-tutorial-67cbd5ec0b00)
- [Zero to JupyterHub with Kubernetes](https://zero-to-jupyterhub.readthedocs.io/en/latest/index.html)

1. Setup a Kubernetes cluster on AWS
    - 先通过一台主机安装kops后，这个主机可以是EC2主机，也可以是本地的linux主机。通过这台主机的kops命令在AWS上创建k8s集群
2. Setup JupyterHub
    - Setting up Helm
    - Setting up JupyterHub
    - Tearing Everything Down
    



# 0. Prerequisites 
1. Redhat EC2 
2. python，pip
3. awscli

Official: [在 Linux 上安装 AWS CLI](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/install-linux.html#install-linux-pip)


### 0.1. 安装Python3
- ```yum install -y python3```
### 0.2. 安装pip
- ```curl -O https://bootstrap.pypa.io/get-pip.py```
- ```python3 get-pip.py --user```
- 确保包含 pip 的文件夹是您的 PATH 变量的一部分
    - ``` ls -a ~```
        ```
        .  ..  .bash_logout  .bash_profile  .bashrc  Desktop  Documents  Downloads
        ```
    - ```export PATH=~/.local/bin:$PATH```
    - ```source ~/.bash_profile```
    - ```pip3 --version```
### 0.3. 通过 pip 安装 AWS CLI 
- ```pip3 install awscli --upgrade --user```
- ```aws --version```
    ```
    aws-cli/1.16.160 Python/3.6.8 Linux/4.18.0-80.el8.x86_64 botocore/1.12.150
    ```







# 1. Kubernetes cluster on AWS

> 先通过一台主机安装kops后，这个主机可以是EC2主机，也可以是本地的linux主机。通过这台主机的kops命令在AWS上创建k8s集群


## 1.1 安装kubectl

- 方法1：https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/install-kubectl.html#install-kubectl-linux

    - ```curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.7/2019-03-27/bin/linux/amd64/kubectl```
    - ```curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl```，最新版本安装

    - ```chmod +x ./kubectl```
    - ```mkdir $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH```
    - ```kubectl version --short --client```

- 方法2：https://github.com/kubernetes/kops/blob/master/docs/install.md#kubectl
    ```
    curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin/kubectl
    ```
## 1.2 安装kops
https://kubernetes.io/docs/setup/custom-cloud/kops/ , https://github.com/kubernetes/kops#installing

We like to think of it as kubectl for clusters. kops helps you create, destroy, upgrade and maintain production-grade, highly available, Kubernetes clusters from the command line. AWS (Amazon Web Services) is currently officially supported, with GCE in beta support , and VMware vSphere in alpha, and other platforms planned.

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
    - error ```-bash: kops: command not found ```, 执行```export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin```

## 1.3 为kops创建IAM账号
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
    
 - 为 kops 用户创建密钥, 接着通过 aws configure 更新 awscli的配置，让它使用新创建的 kops 用户的密钥。并将秘钥导出至环境变量
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
    - ```aws configure```, 配置kops用户秘钥
    - 还需要将 kops 用户的密钥导出到命令行的环境变量，其实是写入 ~/.bashrc 文件
        - ```echo 'export AWS_ACCESS_KEY_ID=.aaaaaaaaa' >> ~/.bashrc```
        - ```echo 'export AWS_SECRET_ACCESS_KEY=bbbbbbbbbbbbbb' >> ~/.bashrc```
        - ```echo 'export AWS_REGION=ap-northeast-2' >> ~/.bashrc```
        
## 1.4 配置S3 Bucket
需要创建一个 S3 bucket，用于存储集群的数据，S3 bucket 命名为 kr.k8s.local-state，同时写到到环境变量 KOPS_STATE_STORE。
        
- ```aws s3api create-bucket --bucket kr.k8s.local-state --create-bucket-configuration LocationConstraint=ap-northeast-2```
- ```echo 'export KOPS_STATE_STORE=s3://kr.k8s.local-state' >> ~/.bashrc```
创建后，可以在 AWS 控制台上看到S3 bucket


## 1.5 创建Kubernetes集群
- 生产ssh，生成的密钥位置 /home/ec2-user/.ssh/id_rsa.pub
    - ```ssh-keygen```
    
- 创建集群的配置文件，**并不会真正地创建集群**
    ```
    kops create cluster \
        --name=kr.k8s.local \
        --zones=ap-northeast-2c  \
        --master-size="t2.micro" \
        --node-size="t2.micro"  \
        --ssh-public-key="~/.ssh/id_rsa.pub"
    ```
    - 为了让kops创建基于gossip的集群，集群的命名需要使用 .k8s.local 作为后缀
    - 地区是ap-northeast-2c，和上面的ap-northeast-2不一样，否则会报错
    
- 创建集群之前，检查集群的配置文件是否正确
    - ```kops edit cluster kr.k8s.local```
- 确认没问题，创建集群
    - ```kops update cluster kr.k8s.local --yes```
    
- 创建集群之后，需要一段时间(5mins)等待集群的初始化，通过 kops validate cluster 来检查集群的创建状态：
    - ```kops validate cluster```，第一次查看状态可能会报错，因为创建过程还没有完成。最终会显示创建成功
    
        ```        
        [root@redhat ~]# kops validate cluster
        Using cluster from kubectl context: kr.k8s.local

        Validating cluster kr.k8s.local

        unexpected error during validation: error listing nodes: Get https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com/api/v1/nodes: EOF
        [root@redhat ~]# kops validate cluster
        Using cluster from kubectl context: kr.k8s.local
        Validating cluster kr.k8s.local
        unexpected error during validation: error listing nodes: Get https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com/api/v1/nodes: EOF
        
        
        [root@redhat ~]# kops validate cluster
        Using cluster from kubectl context: kr.k8s.local
        Validating cluster kr.k8s.local
        INSTANCE GROUPS
        NAME                    ROLE    MACHINETYPE     MIN     MAX     SUBNETS
        master-ap-northeast-2c  Master  t2.micro        1       1       ap-northeast-2c
        nodes                   Node    t2.micro        2       2       ap-northeast-2c
        NODE STATUS
        NAME                                            ROLE    READY
        ip-172-20-40-62.ap-northeast-2.compute.internal master  True
        VALIDATION ERRORS
        KIND    NAME                    MESSAGE
        Machine i-01a3d120a426eaa4f     machine "i-01a3d120a426eaa4f" has not yet joined cluster
        Machine i-0d9d50908d6abbca8     machine "i-0d9d50908d6abbca8" has not yet joined cluster
        Validation Failed
        
        
        [root@redhat ~]# kops validate cluster
        Using cluster from kubectl context: kr.k8s.local

        Validating cluster kr.k8s.local

        INSTANCE GROUPS
        NAME                    ROLE    MACHINETYPE     MIN     MAX     SUBNETS
        master-ap-northeast-2c  Master  t2.micro        1       1       ap-northeast-2c
        nodes                   Node    t2.micro        2       2       ap-northeast-2c

        NODE STATUS
        NAME                                            ROLE    READY
        ip-172-20-36-26.ap-northeast-2.compute.internal node    True
        ip-172-20-38-80.ap-northeast-2.compute.internal node    True
        ip-172-20-40-62.ap-northeast-2.compute.internal master  True

        Your cluster kr.k8s.local is ready
        ```
等待集群起来之后，可以在 AWS 控制台的 UI 上看到新创建的EC2实例。负载均衡里面也可以查到
    
- ```kubectl get nodes``` 来查看节点信息 
    ```
    [root@redhat ~]# kubectl get nodes
    NAME                                              STATUS   ROLES    AGE   VERSION
    ip-172-20-36-26.ap-northeast-2.compute.internal   Ready    node     3m    v1.10.13
    ip-172-20-38-80.ap-northeast-2.compute.internal   Ready    node     3m    v1.10.13
    ip-172-20-40-62.ap-northeast-2.compute.internal   Ready    master   4m    v1.10.13
    ```
- ```kubectl cluster-info``` 来查看集群信息
    ```
    [root@redhat ~]# kubectl cluster-info
    Kubernetes master is running at https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com
    KubeDNS is running at https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
    To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
    ```
    - https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com 即为负载均衡
- 登录到master和node实例上面，查看相关信息
    - ```ssh admin@3.16.188.170 (公有IP 或 公有DNS)```
    - ```kubectl version --short --client```

## 1.6 Kubernetes Dashboard
https://github.com/kubernetes/dashboard


- ```kubectl apply -f  https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml``` 部署dashboard
    ```
    [root@redhat ~]# kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
    secret "kubernetes-dashboard-certs" created
    serviceaccount "kubernetes-dashboard" created
    role.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
    rolebinding.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
    deployment.apps "kubernetes-dashboard" created
    service "kubernetes-dashboard" created
    ```
        
- 获取 kube 和 admin 这两个账户的密码
    - ```kops get secrets kube --type secret -oplaintext```
    - ```kops get secrets admin --type secret -oplaintext```
        ```
        [root@redhat ~]# kops get secrets kube --type secret -oplaintext
        Using cluster from kubectl context: kr.k8s.local

        aaaaaaaaaaaaaaaaaaa
        ```
        ```
        [root@redhat ~]# kops get secrets admin --type secret -oplaintext
        Using cluster from kubectl context: kr.k8s.local

        bbbbbbbbbbbbbbbbbbb
        ```
    - ```kubectl config view``` 查看用户名和密码    
    
- 部署成功后，通过如下的网址访问 Dashboard：
    - https://<master-ip>:<apiserver-port>/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
        - <master-ip> 为 https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com （通过 kubectl cluster-info 命令获得）
        - <apiserver-port> 为 443
        - https://api-kr-k8s-local-9n0gms-400446143.ap-northeast-2.elb.amazonaws.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
    - id: admin, passwd: kube密码（kops get secrets kube --type secret -oplaintext）
    - id: admin, passwd: kube密码（kops get secrets kube --type secret -oplaintext）
    - token choice page will come out and  input admin密码（kops get secrets admin --type secret -oplaintext）
    
# 2. Setup JupyterHub
https://zero-to-jupyterhub.readthedocs.io/en/latest/index.html#setup-jupyterhub

## 2.1 Setting up Helm
Helm, the package manager for Kubernetes, is a useful tool for installing, upgrading and managing applications on a Kubernetes cluster. Helm packages are called charts. We will be installing and managing JupyterHub on our Kubernetes cluster using a Helm chart.

Helm has two parts: a client (helm) and a server (tiller). Tiller runs inside of your Kubernetes cluster as a pod in the kube-system namespace. When you run helm commands, your local Helm client sends instructions to tiller in the cluster that in turn make the requested changes.

- Installation ```curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash```

- Initialization, After installing helm on your machine, initialize Helm on your Kubernetes cluster
    - Set up a ServiceAccount for use by tiller
        - ```kubectl --namespace kube-system create serviceaccount tiller```
    - Give the ServiceAccount full permissions to manage the cluster
        - ```kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller```
    - Initialize helm and tiller
        - ```helm init --service-account tiller --wait```
        - This command only needs to run once per Kubernetes cluster, it will create a tiller deployment in the kube-system namespace and setup your local helm client. This command installs and configures the tiller part of Helm (the whole project, not the CLI) on the remote kubernetes cluster. Later when you want to deploy changes with helm (the local CLI), it will talk to tiller and tell it what to do. tiller then executes these instructions from within the cluster.
- Secure Helm, Ensure that tiller is secure from access inside the cluster:
    - ```kubectl patch deployment tiller-deploy --namespace=kube-system --type=json --patch='[{"op": "add", "path": "/spec/template/spec/containers/0/command", "value": ["/tiller", "--listen=localhost:44134"]}]'```
- Verify, You can verify that you have the correct version and that it installed properly by running:
    - ```helm version```
## 2.2 Setting up JupyterHub
Now that we have a Kubernetes cluster and Helm setup, we can proceed by using Helm to install JupyterHub and related Kubernetes resources using a Helm chart.

- Generate a random hex string representing 32 bytes to use as a security token
    - ```openssl rand -hex 32```
- Create and start editing a file called config.yaml
    - ```nano config.yaml```
        ```
        proxy:
          secretToken: "<RANDOM_HEX>"
        ```
        - paste the generated hex string 
    - Save the config.yaml file by pressing CTRL+X or CMD+X 

- Install JupyterHub
    - ```helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/```
    - ```helm repo update```
- install the chart configured by your config.yaml
    - ```RELEASE=jhub```
    - ```NAMESPACE=jhub```
    - ```
      helm upgrade --install $RELEASE jupyterhub/jupyterhub \
          --namespace $NAMESPACE  \
           --version=0.8.0 \
          --values config.yaml
      ```
      ```
      [root@seoul ~]# helm upgrade --install $RELEASE jupyterhub/jupyterhub \
        >     --namespace $NAMESPACE  \
        >      --version=0.8.0 \
        >     --values config.yaml
        Release "jhub" does not exist. Installing it now.
        NAME:   jhub
        LAST DEPLOYED: Sat May 18 01:59:56 2019
        NAMESPACE: jhub
        STATUS: DEPLOYED

        RESOURCES:
        ==> v1/ConfigMap
        NAME        DATA  AGE
        hub-config  1     0s

        ==> v1/Deployment
        NAME   READY  UP-TO-DATE  AVAILABLE  AGE
        hub    0/1    1           0          0s
        proxy  0/1    1           0          0s

        ==> v1/PersistentVolumeClaim
        NAME        STATUS   VOLUME  CAPACITY  ACCESS MODES  STORAGECLASS  AGE
        hub-db-dir  Pending  gp2     0s

        ==> v1/Pod(related)
        NAME                    READY  STATUS             RESTARTS  AGE
        hub-68847b5544-qnbtm    0/1    Pending            0         0s
        proxy-67cbd47dd9-dzghw  0/1    ContainerCreating  0         0s

        ==> v1/Role
        NAME  AGE
        hub   0s

        ==> v1/RoleBinding
        NAME  AGE
        hub   0s

        ==> v1/Secret
        NAME        TYPE    DATA  AGE
        hub-secret  Opaque  2     0s

        ==> v1/Service
        NAME          TYPE          CLUSTER-IP      EXTERNAL-IP  PORT(S)                     AGE
        hub           ClusterIP     100.65.118.182  <none>       8081/TCP                    0s
        proxy-api     ClusterIP     100.71.161.169  <none>       8001/TCP                    0s
        proxy-public  LoadBalancer  100.66.116.162  <pending>    80:30031/TCP,443:31462/TCP  0s

        ==> v1/ServiceAccount
        NAME  SECRETS  AGE
        hub   1        0s

        ==> v1/StatefulSet
        NAME              READY  AGE
        user-placeholder  0/0    0s

        ==> v1beta1/PodDisruptionBudget
        NAME              MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
        hub               1              N/A              0                    1s
        proxy             1              N/A              0                    1s
        user-placeholder  0              N/A              0                    1s
        user-scheduler    1              N/A              0                    0s


        NOTES:
        Thank you for installing JupyterHub!

        Your release is named jhub and installed into the namespace jhub.

        You can find if the hub and proxy is ready by doing:

         kubectl --namespace=jhub get pod

        and watching for both those pods to be in status 'Ready'.

        You can find the public IP of the JupyterHub by doing:

         kubectl --namespace=jhub get svc proxy-public

        It might take a few minutes for it to appear!

        Note that this is still an alpha release! If you have questions, feel free to
          1. Read the guide at https://z2jh.jupyter.org
          2. Chat with us at https://gitter.im/jupyterhub/jupyterhub
          3. File issues at https://github.com/jupyterhub/zero-to-jupyterhub-k8s/issues
      ```
    - if error
        ```
        Release "jhub" does not exist. Installing it now.
        Error: Chart requires kubernetesVersion: >=1.11.0-0 which is incompatible with Kubernetes v1.10.13
        ```
        - https://github.com/jupyterhub/helm-chart#versions-coupled-to-each-chart-release
        ```
        helm upgrade --install $RELEASE jupyterhub/jupyterhub \
           --namespace $NAMESPACE  \
           --version=0.7.0 \
           --values config.yaml
        ```
- ```kubectl get pod --namespace jhub```
- ```kubectl get service --namespace jhub```
    ```
    NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
    hub            ClusterIP      10.51.243.14    <none>          8081/TCP       1m
    proxy-api      ClusterIP      10.51.247.198   <none>          8001/TCP       1m
    proxy-public   LoadBalancer   10.51.248.230   104.196.41.97   80:31916/TCP   1m
    ```
- ```kubectl describe service proxy-public --namespace jhub```
- access to jupyterhub by EXTERNAL-IP of proxy-publi, any id and pw is ok to login.

## 2.3 Tearing Everything Down
Tearing down your JupyterHub entails:

1. Deleting your Kubernetes namespace, which deletes all objects created and managed by Kubernetes in it.
2. Deleting any cloud resources you’ve requested from the cloud provider.
3. Running a final check to make sure there aren’t any lingering resources that haven’t been deleted (e.g., storage volumes in some cloud providers).

- First, delete the Helm release. This deletes all resources that were created by Helm for your JupyterHub deployment.
    - ```helm delete <YOUR-HELM-RELEASE-NAME> --purge```
        - ```helm delete jhub --purge```
- Next, delete the Kubernetes namespace the hub was installed in. This deletes any disks that may have been created to store user’s data, and any IP addresses that may have been provisioned.
    - ```kubectl delete namespace <YOUR-NAMESPACE>```
        - ```kubectl delete namespace jhub```
        
- Perform the steps in Delete the helm release. These cloud provider agnostic steps will delete the Helm release and the Kubernetes namespace. This must be done before proceeding.
    - https://zero-to-jupyterhub.readthedocs.io/en/latest/turn-off.html#amazon-web-services-aws
        
        
        
        




