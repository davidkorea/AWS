# JupyterHub with Kubernetes on EC2 with my domain
![](https://i.loli.net/2019/05/18/5cdf8f931931195319.png)

STEPS:
1. [Register a domain on Route53]()
2. [Deploy a Kubernetes cluster by kops with Dashboard]()
3. [Deploy JupyterHub on k8s]()

# 1. Register a domain on Route53
### 1. go to Route53
https://console.aws.amazon.com/route53
### 2. select and buy a domain
domain will be available in 15 - 30 mins after the process of buying. 
### 3. test domain 
- ```yum install bind-utils -y```, install ```dig``` command
- ```dig +short davidkorea.com ns```
    ```    
    [root@seoul ~]# dig +short davidkorea.com ns
    ns-725.awsdns-26.net.
    ns-1133.awsdns-13.org.
    ns-1634.awsdns-12.co.uk.
    ns-331.awsdns-41.com.
    ```
- ```dig +short davidkorea.com soa```
    ```    
    [root@seoul ~]# dig +short davidkorea.com soa
    ns-331.awsdns-41.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
    ```
### 4. 创建托管区域并使用subdomain
Hosted Zone + Record Set
1. 创建子域名
    ![](https://i.loli.net/2019/05/18/5cdf9570d62a972617.png)
2. 关联子域名的名称服务器地址至父域名

    - 将子域名的名称服务器地址，复制后，在父域名中创建一条记录，并粘贴子域名的名称服务器地址
        ![](https://i.loli.net/2019/05/18/5cdf96ac891d640630.png)
        ![](https://i.loli.net/2019/05/18/5cdf96b4ad9fe74872.png)
    - 测试连通性
        ```
        [ec2-user@seoul ~]$ dig +short k8s.davidkorea.com ns
        ns-261.awsdns-32.com.
        ns-716.awsdns-25.net.
        ns-1393.awsdns-46.org.
        ns-1817.awsdns-35.co.uk.

        [ec2-user@seoul ~]$ dig +short k8s.davidkorea.com soa
        ns-1817.awsdns-35.co.uk. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
        ```
        - ```dig ns k8s.davidkorea.com```, more detailed info
        - ```dig soa k8s.davidkorea.com```, more detailed info


# 2. Deploy a Kubernetes cluster on AWS

- 使用一台linux或Mac主机，本地或者EC2均可。安装以下工具来部署AWS上的Kubernetes，因为kops只支持linux和mac。
- 所有操作都是在该主机上面，通过kubectl命令来控制AWS上来Kubernetes进行部署

1. [Tools: python pip awscli, kubectl, kops]()
2. [Build Kubernetes cluster]()
3. [Build Kubernetes Dashboard]()

**!!!!! AWARE !!!!!: ROOT权限 sudo -i, 环境变量 export env paras**

## 2.1 Install awscli，kops, kubectl, Tools
Test tools installed or not
- ```kops version```
- ```kubectl version --short --client```
- ```aws --version```
Export env
- ```export PATH=~/.local/bin:$PATH```
- ```export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin```

### 1. awscli
> Official： [在 Linux 上安装 AWS CLI](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/install-linux.html#install-linux-pip)

- Install python3
    - ```yum install -y python3```
- Install pip3
    - ```curl -O https://bootstrap.pypa.io/get-pip.py```, 下载pip安装脚本
    - ```python3 get-pip.py --user```, 使用python3运行脚本,下载安装最新版本的pip和依赖包
    - ```export PATH=~/.local/bin:$PATH```, 不执行会报错"ImportError: cannot import name 'main'"
    - ```source ~/.bash_profile```
    - ```pip --version```
    ```
    [root@seoul ~]# export PATH=~/.local/bin:$PATH
    [root@seoul ~]# pip3 --version
    pip 19.1.1 from /root/.local/lib/python3.6/site-packages/pip (python 3.6)
    ```
- Install AWS CLI by pip
    - ```pip3 install awscli --upgrade --user```
    - ```aws --version```
    ```
    [root@seoul ~]# aws --version
    aws-cli/1.16.160 Python/3.6.8 Linux/4.18.0-80.el8.x86_64 botocore/1.12.150
    ```
### 2. kubectl
> Official:
> - Kubernetes: https://github.com/kubernetes/kops/blob/master/docs/install.md#kubectl
> - AWS：https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/install-kubectl.html#install-kubectl-linux
    
- ```curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl```
- ```chmod +x ./kubectl```
- ```mkdir $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH```, 这一步kubernetes的官方文档没有提到，但是AWS的官方文档（[安装 kubectl](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/install-kubectl.html#install-kubectl-linux)）有提到。还是需要这一步，因为后面kops会有环境变量报错
- ```sudo mv ./kubectl /usr/local/bin/kubectl```, Kubernetes文档没有使用上一步，而是使用了这一步。
- ```kubectl version --short --client```
    ```
    [root@seoul ~]# kubectl version --short --client
    Client Version: v1.14.2
    ```

### 3, kops
> Official: https://github.com/kubernetes/kops#installing

- ```curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64```
- ```chmod +x ./kops```
- ```sudo mv ./kops /usr/local/bin/```
- ```kops version```
    ```
    [root@seoul ~]# kops version
    -bash: kops: command not found
    ```
    - 报错，执行```export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin```
    ```
    [root@seoul ~]# kops version
    Version 1.12.0 (git-a2b7b4a88)
    ```

## 2.2 Deploy Kubernetes cluster on AWS
1. [IAM for kops]()
2. [S3]()
3. [Create, update, delete Kubernetes by kops]()

### 1. IAM
create a user and group named "kops" to give kops access to create VM on AWS. Access includes:
- AmazonEC2FullAccess
- AmazonRoute53FullAccess
- AmazonS3FullAccess
- IAMFullAccess
- AmazonVPCFullAccess
![](https://i.loli.net/2019/05/18/5cdfa6a92f64b10289.png)

- ```aws configure```, fonfigure as the csv downloaded above and will save in /root/.aws/credentials
    ```
    [root@seoul ~]# aws configure
    AWS Access Key ID [None]: aaaaaaaaaaaaaaa
    AWS Secret Access Key [None]: bbbbbbbbbbbbbbbbbbbbbb
    Default region name [None]:
    Default output format [None]:
    ```
### 2. S3
- name: ```kops.k8s.davidkorea.com```
- region: ```seoul```
- the rest all by default

![](https://i.loli.net/2019/05/18/5cdfa793d819c72570.png)

### 3. Create Kubernetes by kops on AWS
The below command will generate a cluster configuration, but not start building it. Make sure that you have generated SSH key pair before creating the cluster.
1. SSH
```ssh-keygen```, 此SSH key将用于远程连接 kops创建的AWS虚拟机

2. kops create cluster，仅生成配置文件，并不会真正创建kubernetes集群
```
kops create cluster \
--name=k8s.davidkorea.com \
--state=s3://kops.k8s.davidkorea.com \
--zones=ap-northeast-2c \
--node-count=2 \
--node-size=t2.medium \
--master-size=t2.medium \
--dns-zone=k8s.davidkorea.com
```
- ```--name```，k8s集群名称
- ```--state```，S3存储位置
- ```--dns-zone```，子域名subdomain
    
如配置不正确还可以修改配置文件
```
kops edit cluster k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com
```
其实际报错在 Amazon S3/kops.k8s.davidkorea.com/k8s.davidkorea.com/config

3. 真正创建kubernetes集群
    - ```kops update cluster k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com --yes```
        - ```--state```参数必须要指定，因为这不是grossip方式

    - validate, 一定要有state参数 
        - ```kops validate cluster --state=s3://kops.k8s.davidkorea.com```
        ```
        [root@seoul ~]# kops validate cluster --state=s3://kops.k8s.davidkorea.com
        Using cluster from kubectl context: k8s.davidkorea.com

        Validating cluster k8s.davidkorea.com

        INSTANCE GROUPS
        NAME			ROLE	MACHINETYPE	MIN	MAX	SUBNETS
        master-ap-northeast-2c	Master	t2.medium	1	1	ap-northeast-2c
        nodes			Node	t2.medium	2	2	ap-northeast-2c

        NODE STATUS
        NAME							ROLE	READY
        ip-172-20-36-35.ap-northeast-2.compute.internal		master	True
        ip-172-20-41-77.ap-northeast-2.compute.internal		node	True
        ip-172-20-48-127.ap-northeast-2.compute.internal	node	True

        VALIDATION ERRORS
        KIND	NAME					MESSAGE
        Pod	kube-system/kube-dns-57dd96bb49-28p5q	kube-system pod "kube-dns-57dd96bb49-28p5q" is not ready (kubedns)

        Validation Failed

        -----
        Your cluster k8s.davidkorea.com is ready    
        ```
4. 查看集群和各nodes状态
```
[root@seoul ~]# kubectl get nodes
NAME                                               STATUS   ROLES    AGE     VERSION
ip-172-20-36-35.ap-northeast-2.compute.internal    Ready    master   5m52s   v1.12.7
ip-172-20-41-77.ap-northeast-2.compute.internal    Ready    node     4m51s   v1.12.7
ip-172-20-48-127.ap-northeast-2.compute.internal   Ready    node     4m49s   v1.12.7
```
```
[root@seoul ~]#  kubectl cluster-info
Kubernetes master is running at https://api.k8s.davidkorea.com       # msater的域名
KubeDNS is running at https://api.k8s.davidkorea.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
5. EC2中增加了3个VM

master: api.k8s.davidkorea.com
```
公有 DNS (IPv4): ec2-13-209-22-79.ap-northeast-2.compute.amazonaws.com
IPv4 公有 IP: 13.209.22.79
私有 DNS: ip-172-20-36-35.ap-northeast-2.compute.internal
私有 IP: 172.20.36.35
```
nodes
```
公有 DNS (IPv4): ec2-54-180-104-89.ap-northeast-2.compute.amazonaws.com
IPv4 公有 IP: 54.180.104.89
私有 DNS: ip-172-20-41-77.ap-northeast-2.compute.internal
私有 IP: 172.20.41.77
```
```
公有 DNS (IPv4): ec2-54-180-105-142.ap-northeast-2.compute.amazonaws.com
IPv4 公有 IP: 54.180.105.142
私有 DNS: ip-172-20-48-127.ap-northeast-2.compute.internal
私有 IP: 172.20.48.127
```
6. Delete 删除k8s集群部署
    - ```kops delete cluster --name=k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com```
    
## 2.3 Dashboard
> Official: https://github.com/kubernetes/dashboard
> - 重点参考以下文档
>   - [AWS EC2 上安装 Kubernetes 的示例](https://www.jianshu.com/p/7d448abae039)
>   - [Setting Up a Kubernetes Cluster on AWS in 5 Minutes](https://ramhiser.com/post/2018-05-20-setting-up-a-kubernetes-cluster-on-aws-in-5-minutes/)

Run the below codes on the host that installed kops, NOT master node.

1. 创建deployment和service
    - ```kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml```, 官方yaml文件包含了deployment和service
2. 创建用户
    - ```kops get secrets kube --type secret -oplaintext --state=s3://kops.k8s.davidkorea.com```, 需要指定state，保存生成的key
    - ```kops get secrets admin --type secret -oplaintext --state=s3://kops.k8s.davidkorea.com```, 需要指定state，保存生成的key

3. 访问dashboard

- visit "https://<master-ip>:<apiserver-port>/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/"
    - “<master-ip>” can get by ```kubectl cluster-info```
    - "<apiserver-port>" is 443
- ```https://api.k8s.davidkorea.com:443/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/```
    - 反正一开始也是登录不了，来来回回总是提示输入用户名和密码
    - 中间来回试了下```https://api.k8s.davidkorea.com/ui```，这个地址输入用户名密码后直接会返回一个api的json页面，也是无法进入dashboard
    - 然后再试回第一个url，再登陆时，用户名密码提示框下面就有dashboard的影子了
    - 根据之前测试的，两次输入id=admin，passwd=kube-secret，然后token使用admin-secret，登陆成功




# 3. JupyterHub

- follow below steps
- access jupyterhub url
    ```
    [root@seoul ~]# kubectl get svc -n jhub
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP                                                                   PORT(S)                      AGE
    hub            ClusterIP      100.65.118.182   <none>                                                                        8081/TCP                     25m
    proxy-api      ClusterIP      100.71.161.169   <none>                                                                        8001/TCP                     25m
    proxy-public   LoadBalancer   100.66.116.162   ab98a5e2c791011e994340ae990a9697-517512224.ap-northeast-2.elb.amazonaws.com   80:30031/TCP,443:31462/TCP   25m
    [root@seoul ~]# kubectl cluster-info
    ```
    - proxy-public EXTERNAL-IP 可以直接访问
    - 而我们肯定要使用自己的域名，买都买了
        - 所以将上米啊的PORTS转发，添加到master节点的入站规则。30031和31462，测试http30031成功，https31462还不行

