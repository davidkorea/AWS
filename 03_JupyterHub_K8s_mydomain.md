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

    - 如配置不正确还可以修改配置文件，文件保存在 Amazon S3/kops.k8s.davidkorea.com/k8s.davidkorea.com/config
        ```
        kops edit cluster k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com
        ```

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

        ================================ run again =====================================
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

    - master: api.k8s.davidkorea.com
        ```
        公有 DNS (IPv4): ec2-13-209-22-79.ap-northeast-2.compute.amazonaws.com
        IPv4 公有 IP: 13.209.22.79
        私有 DNS: ip-172-20-36-35.ap-northeast-2.compute.internal
        私有 IP: 172.20.36.35
        ```
    - nodes
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
    ![](https://i.loli.net/2019/05/18/5cdfb14c181b985782.png)



# 3. JupyterHub
> Official: https://zero-to-jupyterhub.readthedocs.io/en/latest/index.html#setup-jupyterhub

1. [Setting up Helm]()
2. [Setting up JupyterHub]()
3. [Tearing Everything Down]()

These codes still run on the host which installed kops. All settings followed by official manual.

## 3.1 Setting up Helm
> Helm, the package manager for Kubernetes, is a useful tool for installing, upgrading and managing applications on a Kubernetes cluster. Helm packages are called charts. We will be installing and managing JupyterHub on our Kubernetes cluster using a Helm chart.
> Helm has two parts: a client (helm) and a server (tiller). 
>   - Tiller runs inside of your Kubernetes cluster as a pod in the kube-system namespace. 
>   - When you run helm commands, your local Helm client sends instructions to tiller in the cluster that in turn make the requested changes.

### 1. Installation 
- ```curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash```
    - 安装了Helm的客户端程序在安装了kops的本地主机
### 2. Initialization
After installing helm on your machine, initialize Helm on your Kubernetes cluster。在本机安装了helm的客户端命令后，再在远端的k8s集群中安装helm的服务端tiller
- Set up a ServiceAccount for use by tiller
    - ```kubectl --namespace kube-system create serviceaccount tiller```
- Give the ServiceAccount full permissions to manage the cluster
    - ```kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller```
- Initialize helm and tiller
    - ```helm init --service-account tiller --wait```
    - This command only needs to run once per Kubernetes cluster, it will create a tiller deployment in the kube-system namespace and setup your local helm client. 
    - This command installs and configures the tiller part of Helm (the whole project, not the CLI) on the remote kubernetes cluster. 
    - Later when you want to deploy changes with helm (the local CLI), it will talk to tiller and tell it what to do. tiller then executes these instructions from within the cluster.
### 3. Secure Helm
- Ensure that tiller is secure from access inside the cluster:
    - ```kubectl patch deployment tiller-deploy --namespace=kube-system --type=json --patch='[{"op": "add", "path": "/spec/template/spec/containers/0/command", "value": ["/tiller", "--listen=localhost:44134"]}]'```
### 4. Verify
- You can verify that you have the correct version and that it installed properly by running:
    - ```helm version```
    
## 3.2 Setting up JupyterHub
Now that we have a Kubernetes cluster and Helm setup, we can proceed by using Helm to install JupyterHub and related Kubernetes resources using a Helm chart.

1. Generate a random hex string representing 32 bytes to use as a security token
    - ```openssl rand -hex 32```
2. Create and start editing a file called config.yaml，paste the generated hex string 
    - ```nano config.yaml```
        ```
        proxy:
          secretToken: "<RANDOM_HEX>"
        ```
    - Save the config.yaml file by pressing CTRL+X or CMD+X 
    - 此处用vim也完全可以
3. Install JupyterHub
    - ```helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/```
    - ```helm repo update```
4. install the chart configured by your config.yaml
    - ```RELEASE=jhub```
    - ```NAMESPACE=jhub```
    - ```
      helm upgrade --install $RELEASE jupyterhub/jupyterhub \
          --namespace $NAMESPACE  \
           --version=0.8.0 \
          --values config.yaml
      ```
5. check deploy svc and pod
    ```
    [root@seoul ~]# kubectl get deploy -n jhub
    NAME    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    hub     1         1         1            1           6h27m
    proxy   1         1         1            1           6h27m
    
    [root@seoul ~]# kubectl get po -n jhub
    NAME                     READY   STATUS    RESTARTS   AGE
    hub-68847b5544-qnbtm     1/1     Running   0          6h27m
    jupyter-admin            1/1     Running   0          39m
    proxy-67cbd47dd9-dzghw   1/1     Running   0          6h27m
    
    [root@seoul ~]# kubectl get svc -n jhub
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                        AGE
    hub            ClusterIP      100.65.118.182   <none>           8081/TCP                       6h28m
    proxy-api      ClusterIP      100.71.161.169   <none>           8001/TCP                       6h28m
    proxy-public   LoadBalancer   100.66.116.162   ab98a5e2c791011e994340ae990a9697-517512224.ap-northeast-2.elb.amazonaws.com                                                                     80:30031/TCP,443:31462/TCP     6h28m
    ```
## 3.3 访问JupyterHub
可以看到，helm创建了LoadBalancer类型的service。如果非云环境，Nodeport类型即可实现pod的外网访问。
### 1. 通过域名绑定负载均衡器来访问（推荐）
虽然负载均衡器的EXTERNAL-IP可以直接访问，但是太丑了。域名都买了，一定要绑定自己的域名来使用。
- Route53 - subdomain
    - 在Subdomain：k8s.davidkorea.com 中添加一条记录，类型为IPv4地址
    ![](https://i.loli.net/2019/05/18/5cdfb8865992030767.png)
    
- 访问 http://jupyter.k8s.davidkorea.com/ 即可
    ![](https://i.loli.net/2019/05/18/5cdfb974c46e457356.png)


### 2. 通过Master节点访问
master域名：api.k8s.davidkorea.com, 可以通过```kubectl cluster-info```查到
```
[root@seoul ~]# kubectl get svc -n jhub
NAME           TYPE           CLUSTER-IP       EXTERNAL-IP       PORT(S)          AGE
hub            ClusterIP      100.65.118.182   <none>                                                                        8081/TCP                     25m
proxy-api      ClusterIP      100.71.161.169   <none>                                                                        8001/TCP                     25m
proxy-public   LoadBalancer   100.66.116.162   ab98a5e2c791011e994340ae990a9697-517512224.ap-northeast-2.elb.amazonaws.com   80:30031/TCP,443:31462/TCP   25m
```
- 可以看到上面proxy-public的端口转发
    - pod 80 -> master 30031
    - pod 443 -> master 31462

- 开放 k8s master节点的inbound端口30031和31462
    - 测试http30031成功，http://api.k8s.davidkorea.com:30031
    - https31462还不行, https://api.k8s.davidkorea.com:31462

![](https://i.loli.net/2019/05/18/5cdfb7c41d94886722.png)

## 3.4 Tearing Everything Down
https://zero-to-jupyterhub.readthedocs.io/en/latest/turn-off.html

### 1. Delete the helm release
- ```  helm delete <YOUR-HELM-RELEASE-NAME> --purge```
    - ```helm delete jhub --purge```
    - ```helm list```, the name is <YOUR-HELM-RELEASE-NAME>=jhub
- ```kubectl delete namespace <YOUR-NAMESPACE>```
    - ```kubectl delete namespace jhub```
    
    
```
[root@seoul ~]# helm delete jhub --purge
release "jhub" deleted
[root@seoul ~]# kubectl get deploy
No resources found.
[root@seoul ~]# kubectl get deploy -n jhub
No resources found.
[root@seoul ~]# kubectl get svc -n jhub
No resources found.

[root@seoul ~]# kubectl get ns
NAME          STATUS   AGE
default       Active   12h
jhub          Active   11h
kube-public   Active   12h
kube-system   Active   12h

[root@seoul ~]# kubectl delete namespace jhub
namespace "jhub" deleted
```
### 2. AWS

- ```kops delete cluster <CLUSTER-NAME> --yes```
    - ```<CLUSTER-NAME>``` is k8s.davidkorea.com
    - whther needs ```--state```?? !! 需要
- ```aws ec2 stop-instances --instance-ids <aws-instance id of CI host>```
- ```aws ec2 terminate-instances --instance-ids <aws-instance id of CI host>```

```
[root@seoul ~]# kops delete cluster k8s.davidkorea.com

State Store: Required value: Please set the --state flag or export KOPS_STATE_STORE.
For example, a valid value follows the format s3://<bucket>.
You can find the supported stores in https://github.com/kubernetes/kops/blob/master/docs/state.md.

[root@seoul ~]# kops delete cluster k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com --yes
TYPE			NAME							ID
autoscaling-config	master-ap-northeast-2c.masters.k8s.davidkorea.com-20190518004904		master-ap-northeast-2c.masters.k8s.davidkorea.com-20190518004904
autoscaling-config	nodes.k8s.davidkorea.com-20190518004904			nodes.k8s.davidkorea.com-20190518004904
autoscaling-group	master-ap-northeast-2c.masters.k8s.davidkorea.com	master-ap-northeast-2c.masters.k8s.davidkorea.com
autoscaling-group	nodes.k8s.davidkorea.com				nodes.k8s.davidkorea.com
dhcp-options		k8s.davidkorea.com					dopt-07a79a6aaf96e13d0
iam-instance-profile	masters.k8s.davidkorea.com				masters.k8s.davidkorea.com
iam-instance-profile	nodes.k8s.davidkorea.com				nodes.k8s.davidkorea.com
iam-role		masters.k8s.davidkorea.com				masters.k8s.davidkorea.com
iam-role		nodes.k8s.davidkorea.com				nodes.k8s.davidkorea.com
instance		master-ap-northeast-2c.masters.k8s.davidkorea.com	i-0ce8c1735bd7e76be
instance		nodes.k8s.davidkorea.com				i-0389713798a253329
instance		nodes.k8s.davidkorea.com				i-0a782f9d8a191a97b
internet-gateway	k8s.davidkorea.com					igw-0ed2a9d6cf7a829e6
keypair			kubernetes.k8s.davidkorea.com-9c:9d:38:01:6b:e1:8a:ba:4f:fd:cc:e7:69:6d:44:a8	kubernetes.k8s.davidkorea.com-9c:9d:38:01:6b:e1:8a:ba:4f:fd:cc:e7:69:6d:44:a8
route-table		k8s.davidkorea.com					rtb-05993fc55c5a91867
route53-record		api.internal.k8s.davidkorea.com.			Z2JIXKMGDI8GAW/api.internal.k8s.davidkorea.com.
route53-record		api.k8s.davidkorea.com.					Z2JIXKMGDI8GAW/api.k8s.davidkorea.com.
security-group		masters.k8s.davidkorea.com				sg-096e936e32baefe2e
security-group		nodes.k8s.davidkorea.com				sg-0124c339ae352da03
subnet			ap-northeast-2c.k8s.davidkorea.com			subnet-0a4435540390e0d9d
volume			c.etcd-events.k8s.davidkorea.com			vol-0b7f8a74dc64bc360
volume			c.etcd-main.k8s.davidkorea.com				vol-0d52dea9995920d33
vpc			k8s.davidkorea.com					vpc-02743af4de5ac4884

keypair:kubernetes.k8s.davidkorea.com-9c:9d:38:01:6b:e1:8a:ba:4f:fd:cc:e7:69:6d:44:a8	ok
autoscaling-group:nodes.k8s.davidkorea.com	ok
autoscaling-group:master-ap-northeast-2c.masters.k8s.davidkorea.com	ok
instance:i-0ce8c1735bd7e76be	ok
instance:i-0389713798a253329	ok
internet-gateway:igw-0ed2a9d6cf7a829e6	still has dependencies, will retry
instance:i-0a782f9d8a191a97b	ok
route53-record:Z2JIXKMGDI8GAW/api.internal.k8s.davidkorea.com.	ok
iam-instance-profile:masters.k8s.davidkorea.com	ok
iam-instance-profile:nodes.k8s.davidkorea.com	ok
iam-role:masters.k8s.davidkorea.com	ok
iam-role:nodes.k8s.davidkorea.com	ok
subnet:subnet-0a4435540390e0d9d	still has dependencies, will retry
autoscaling-config:nodes.k8s.davidkorea.com-20190518004904	ok
autoscaling-config:master-ap-northeast-2c.masters.k8s.davidkorea.com-20190518004904	ok
volume:vol-0d52dea9995920d33	still has dependencies, will retry
volume:vol-0b7f8a74dc64bc360	still has dependencies, will retry
security-group:sg-096e936e32baefe2e	still has dependencies, will retry
security-group:sg-0124c339ae352da03	still has dependencies, will retry
Not all resources deleted; waiting before reattempting deletion
	security-group:sg-096e936e32baefe2e
	subnet:subnet-0a4435540390e0d9d
	volume:vol-0b7f8a74dc64bc360
	vpc:vpc-02743af4de5ac4884
	dhcp-options:dopt-07a79a6aaf96e13d0
	route-table:rtb-05993fc55c5a91867
	security-group:sg-0124c339ae352da03
	internet-gateway:igw-0ed2a9d6cf7a829e6
	volume:vol-0d52dea9995920d33
subnet:subnet-0a4435540390e0d9d	still has dependencies, will retry
volume:vol-0d52dea9995920d33	still has dependencies, will retry
volume:vol-0b7f8a74dc64bc360	still has dependencies, will retry
internet-gateway:igw-0ed2a9d6cf7a829e6	still has dependencies, will retry
security-group:sg-096e936e32baefe2e	still has dependencies, will retry
security-group:sg-0124c339ae352da03	still has dependencies, will retry
Not all resources deleted; waiting before reattempting deletion
	volume:vol-0d52dea9995920d33
	dhcp-options:dopt-07a79a6aaf96e13d0
	route-table:rtb-05993fc55c5a91867
	security-group:sg-0124c339ae352da03
	internet-gateway:igw-0ed2a9d6cf7a829e6
	subnet:subnet-0a4435540390e0d9d
	volume:vol-0b7f8a74dc64bc360
	security-group:sg-096e936e32baefe2e
	vpc:vpc-02743af4de5ac4884
subnet:subnet-0a4435540390e0d9d	still has dependencies, will retry
volume:vol-0b7f8a74dc64bc360	ok
internet-gateway:igw-0ed2a9d6cf7a829e6	still has dependencies, will retry
volume:vol-0d52dea9995920d33	ok
security-group:sg-0124c339ae352da03	still has dependencies, will retry
security-group:sg-096e936e32baefe2e	ok
Not all resources deleted; waiting before reattempting deletion
	vpc:vpc-02743af4de5ac4884
	dhcp-options:dopt-07a79a6aaf96e13d0
	route-table:rtb-05993fc55c5a91867
	security-group:sg-0124c339ae352da03
	internet-gateway:igw-0ed2a9d6cf7a829e6
	subnet:subnet-0a4435540390e0d9d
subnet:subnet-0a4435540390e0d9d	ok
internet-gateway:igw-0ed2a9d6cf7a829e6	ok
security-group:sg-0124c339ae352da03	ok
route-table:rtb-05993fc55c5a91867	ok
vpc:vpc-02743af4de5ac4884	ok
dhcp-options:dopt-07a79a6aaf96e13d0	ok
Deleted kubectl config for k8s.davidkorea.com

Deleted cluster: "k8s.davidkorea.com"
```
- terminated k8s cluster 3 EC2 instances
- clear all data in S3
- delete S3 bucket manually
![](https://i.loli.net/2019/05/18/5ce010406768f57410.png)
