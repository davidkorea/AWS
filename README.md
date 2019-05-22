# AWS





https://ramhiser.com/post/2018-05-20-setting-up-a-kubernetes-cluster-on-aws-in-5-minutes/
https://ramhiser.com/post/2018-05-28-adding-dask-and-jupyter-to-kubernetes-cluster/





02_Redhat_k8s.md


https://www.jianshu.com/p/7d448abae039

https://www.baidu.com/link?url=t-0eA8AGGRWQ_MQpHfqZxBb5459EheTESNPU_rRKy2cPvqZCEoP6y6Ojs6zIEUjW&wd=&eqid=d285ac5b0000051c000000065cd92146

```

[root@server ~]# kops create cluster \
>     --name=cluster.k8s.local \
>     --zones=ap-northeast-2  \
>     --master-size="t2.micro" \
>     --node-size="t2.micro"  \
>     --ssh-public-key="~/.ssh/id_rsa.pub"
I0513 09:27:34.345425   15428 create_cluster.go:1351] Using SSH public key: /root/.ssh/id_rsa.pub

unable to infer CloudProvider from Zones (is there a typo in --zones?)
[root@server ~]#
[root@server ~]# kops create cluster \
>     --name=cluster.k8s.local \
>     --zones=ap-northeast-2c  \
>     --master-size="t2.micro" \
>     --node-size="t2.micro"  \
>     --ssh-public-key="~/.ssh/id_rsa.pub"
```
-> https://github.com/kubernetes/kops/issues/3784#issuecomment-342324557
-----

# 3. 安装 kops
Official manual: [Installing Kubernetes on AWS with kops](https://kubernetes.io/docs/setup/custom-cloud/kops/)
1. ```wget https://github.com/kubernetes/kops/releases/download/1.10.0/kops-linux-amd64```
2. ```chmod +x kops-linux-amd64```
3. ```mv kops-linux-amd64 /usr/local/bin/kops```
4. ```kops version```失败，执行以下命令后成功，但重启后失效
    - ```export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin```




-----

# 2. 安装 kubectl
Official manual: [安装 kubectl](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/install-kubectl.html#install-kubectl-linux)
1. ```curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.12.7/2019-03-27/bin/linux/amd64/kubectl```
2. ```chmod +x ./kubectl```
3. ```mkdir $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH```
4. ```echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc```
5. ```kubectl version --short --client```





-----

# 1. EC2 Redhat 安装aws cli

Official manual: [安装aws cli](https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/install-linux.html#install-linux-pip)
1. 安装Python3
    - ```yum install -y python3```
2. 安装pip
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
3. 通过 pip 安装 AWS CLI 
    - ```pip3 install awscli --upgrade --user```
    - ```aws --version```
        ```
        aws-cli/1.16.157 Python/3.6.8 Linux/4.18.0-80.el8.x86_64 botocore/1.12.147
        ```
-----
























**下面又不行了，还是按照上面官方文档**
1. install epel-release
    - centos 可以直接yum安装
    - redhat需要手动安装 ```rpm -ivh http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm```
2. ```yum -y install python-pip```, epel first
3. ```pip install awscli```
4. ```aws configure```
    - 先创建액세스 키(액세스 키 ID 및 비밀 액세스 키)，下载公钥和私钥文件
    ```        
    [root@server ~]# aws configure
    AWS Access Key ID [None]: 
    AWS Secret Access Key [None]: 
    Default region name [None]: ap-northeast-2
    Default output format [None]: json
    ```
    - 使用 AWS CLI 时，必须明确指定或通过设置默认区域来指定 AWS 区域。有关可用区域的列表，请参阅区域和终端节点。AWS CLI 使用的区域指示符与您在 AWS 管理控制台 URL 和服务终端节点中看到的名称相同。https://docs.aws.amazon.com/zh_cn/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration
5. 

4. http://docs.amazonaws.cn/AWSEC2/latest/UserGuide/UsingIAM.html


- create S3
```
[root@server ~]# aws s3api create-bucket --bucket k8s.s3 --create-bucket-configuration LocationConstraint=ap-northeast-2
{
    "Location": "http://k8s.s3.s3.amazonaws.com/"
}
```


















———-01_Redhat_docker_httpd.md

