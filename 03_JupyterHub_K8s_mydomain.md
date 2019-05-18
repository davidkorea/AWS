# JupyterHub with Kubernetes on EC2 with my domain
![](https://i.loli.net/2019/05/18/5cdf8f931931195319.png)

STEPS:
1. Register a domain on Route53
2. Deploy a Kubernetes cluster by kops with Dashboard
3. Deploy JupyterHub on k8s

# 1. Register a domain on Route53
### 1. go to https://console.aws.amazon.com/route53
### 2. select and buy a domain. domain will be available in 15 - 30 mins after the process of buying. 
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
### 4. Create new hosted zone 创建托管区域并使用subdomain   
![](https://i.loli.net/2019/05/18/5cdf9570d62a972617.png)
    
    
    
    
    

- full info
    ```    
    [root@seoul ~]# dig ns k8s.davidkorea.com

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-17.P2.el8_0 <<>> ns k8s.davidkorea.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 50109
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;k8s.davidkorea.com.            IN      NS

    ;; ANSWER SECTION:
    k8s.davidkorea.com.     60      IN      NS      ns-261.awsdns-32.com.
    k8s.davidkorea.com.     60      IN      NS      ns-716.awsdns-25.net.
    k8s.davidkorea.com.     60      IN      NS      ns-1393.awsdns-46.org.
    k8s.davidkorea.com.     60      IN      NS      ns-1817.awsdns-35.co.uk.

    ;; Query time: 67 msec
    ;; SERVER: 172.31.0.2#53(172.31.0.2)
    ;; WHEN: Fri May 17 08:11:10 UTC 2019
    ;; MSG SIZE  rcvd: 184

    [root@seoul ~]# dig soa k8s.davidkorea.com

    ; <<>> DiG 9.11.4-P2-RedHat-9.11.4-17.P2.el8_0 <<>> soa k8s.davidkorea.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 40279
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;k8s.davidkorea.com.            IN      SOA

    ;; ANSWER SECTION:
    k8s.davidkorea.com.     60      IN      SOA     ns-1817.awsdns-35.co.uk. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

    ;; Query time: 104 msec
    ;; SERVER: 172.31.0.2#53(172.31.0.2)
    ;; WHEN: Fri May 17 08:11:27 UTC 2019
    ;; MSG SIZE  rcvd: 131
    ```
- Hosted Zone + Record Set
    ```    
    [root@seoul ~]# dig +short k8s.davidkorea.com ns
    ns-1393.awsdns-46.org.
    ns-1817.awsdns-35.co.uk.
    ns-261.awsdns-32.com.
    ns-716.awsdns-25.net.
    [root@seoul ~]# dig +short k8s.davidkorea.com soa
    ns-1817.awsdns-35.co.uk. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
    ```




# Aware: sudo -i, export env paras
- ```kops version```
- ```kubectl version --short --client```
- ```aws```
# Install kops, kubectl, awscli

```
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x ./kops
sudo mv ./kops /usr/local/bin/
kops version
```
- error -bash: kops: command not found, 
    - 执行```export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin```
    
```
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

# IAM
- user
- add user to group
    - AmazonEC2FullAccess, AmazonRoute53FullAccess, AmazonS3FullAccess, IAMFullAccess, AmazonVPCFullAccess
- get public and private credential key
- ```aws configure```, save in /root/.aws/credentials
    ```
    [root@seoul ~]# aws configure
    AWS Access Key ID [None]: aaaaaaaaaaaaaaa
    AWS Secret Access Key [None]: bbbbbbbbbbbbbbbbbbbbbb
    Default region name [None]:
    Default output format [None]:
    ```
# Route53 (
# S3
- name: kops.k8s.davidkorea.com
- region: seoul
- the rest all by default

# SSH
```ssh-keygen```

# kops create cluster
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
- ```kops update cluster k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com --yes```

- delete
    - ```kops delete cluster --name=k8s.davidkorea.com --state=s3://kops.k8s.davidkorea.com```
    
- validate, 一定要有state参数 ```kops validate cluster --state=s3://kops.k8s.davidkorea.com```
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
    ```
    [root@seoul ~]# kubectl get nodes
    NAME                                               STATUS   ROLES    AGE     VERSION
    ip-172-20-36-35.ap-northeast-2.compute.internal    Ready    master   5m52s   v1.12.7
    ip-172-20-41-77.ap-northeast-2.compute.internal    Ready    node     4m51s   v1.12.7
    ip-172-20-48-127.ap-northeast-2.compute.internal   Ready    node     4m49s   v1.12.7
    ```
    ```
    [root@seoul ~]#  kubectl cluster-info
    Kubernetes master is running at https://api.k8s.davidkorea.com # msater的域名
    KubeDNS is running at https://api.k8s.davidkorea.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

    To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
    ```
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

# Dashboard
- run codes on the host that install kops, NOT master node
- ```
- ```kops get secrets kube --type secret -oplaintext --state=s3://kops.k8s.davidkorea.com```, 需要指定state
- ```kops get secrets admin --type secret -oplaintext --state=s3://kops.k8s.davidkorea.com```, 需要指定state

- visit ```https://api.k8s.davidkorea.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/```
    - 反正一开始也是登录不了，来来回回总是提示输入用户名和密码
    - 中间来回试了下```https://api.k8s.davidkorea.com/ui```，这个地址输入用户名密码后直接会返回一个api的json页面，也是无法进入dashboard
    - 然后再试回第一个url，再登陆时，用户名密码提示框下面就有dashboard的影子了
    - 根据之前测试的，两次输入id=admin，passwd=kube-secret，然后token使用admin-secret，登陆成功

# hupyterhub

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

