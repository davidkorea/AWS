# Docker Networking
## 2. Multi-Nodes Overlay 
[Mutil-host networking with etcd](https://github.com/davidkorea/AWS/blob/master/DOCS/docker-overlay-network.md)

> 反正就是容器互相ping不通，etcd cluster 正常，能够同时生成overlay网络。测试了三四遍，都不行，EC2 不支持吗？？？？？


- 每个node上的docker0桥都是172.17.0.0/24
- 若要不同主机上的docker容器通信，IP地址一定不能一样
- 而不同节点之间如何保证ip互不冲突？ etcd存储

EC2 Redhat:
- node1: eth0 172.31.27.35
- node2: eto0 172.31.25.135
- enable all tcp ports inbound, or ./etcdctl cluster-health refused

### 所有节点下载安装etcd
```
wget https://github.com/coreos/etcd/releases/download/v3.3.13/etcd-v3.3.13-linux-amd64.tar.gz
tar zxvf etcd-v3.3.13-linux-amd64.tar.gz
```
在docker-node1上

```
[root@node1 etcd-v3.3.13-linux-amd64]#
[root@node1 etcd-v3.3.13-linux-amd64]# nohup ./etcd --name node1 --initial-advertise-peer-urls http://172.31.27.35:2380 \
 --listen-peer-urls http://172.31.27.35:2380 \
 --listen-client-urls http://172.31.27.35:2379,http://127.0.0.1:2379 \
 --advertise-client-urls http://172.31.27.35:2379 \
 --initial-cluster-token etcd-cluster \
 --initial-cluster node1=http://172.31.27.35:2380,node2=http://172.31.25.135:2380 \
 --initial-cluster-state new&
[1] 6337
[root@node1 etcd-v3.3.13-linux-amd64]# nohup: ignoring input and appending output to 'nohup.out'
```
在docker-node2上

```
[root@node2 etcd-v3.3.13-linux-amd64]# nohup ./etcd --name node2 --initial-advertise-peer-urls http://172.31.25.135:2380 \
 --listen-peer-urls http://172.31.25.135:2380 \
 --listen-client-urls http://172.31.25.135:2379,http://127.0.0.1:2379 \
 --advertise-client-urls http://172.31.25.135:2379 \
 --initial-cluster-token etcd-cluster \
 --initial-cluster node1=http://172.31.27.35:2380,node2=http://172.31.25.135:2380 \
 --initial-cluster-state new&
[1] 15239
[root@node2 etcd-v3.3.13-linux-amd64]# nohup: ignoring input and appending output to 'nohup.out'
```

```
[root@node1 etcd-v3.3.13-linux-amd64]# ./etcdctl cluster-health
member 181a4637330a1c96 is healthy: got healthy result from http://172.31.29.85:2379
member ceb57aa75f03a66c is healthy: got healthy result from http://172.31.25.33:2379
cluster is healthy
```
### 重启docker服务


在docker-node1上

```
[root@node1 ~]# service docker stop
[root@node1 ~]# /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://172.31.27.35:2379 --cluster-advertise=172.31.27.35:2375&
```

在docker-node2上

```
[root@node2 ~]# service docker stop
[root@node2 ~]# /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://172.31.25.135:2379 --cluster-advertise=172.31.25.135:2375&
```
### 创建overlay network

在docker-node1上创建一个demo的overlay network

```
[root@node1 ~]# docker network create -d overlay demo
aff0321d7cba010d1adb8b0cafb2ec72b301fea9649bd773e7e0d1701c4c6c76
[root@node1 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
7f69f31c58c4        bridge              bridge              local
aff0321d7cba        demo                overlay             global
4045ea3034c2        host                host                local
a2f91aeb97da        none                null                local

[root@node1 ~]# docker network inspect demo      # more detailed network info
```
在node2上，这个demo的overlay network会被同步创建
```
[root@node2 ~]#
[root@node2 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
0c9248ebca38        bridge              bridge              local
aff0321d7cba        demo                overlay             global
bb12f294362b        host                host                local
fe9e0c36fe87        none                null                local
```
### 创建连接demo网络的容器

在docker-node1上








## 1. One Node
1. docker之间的网络类似于 netns，通过一个veth pair网卡
    - 一个在doucker内部，一个连接到ip为172.17.0.1的docker0桥上
    - docker0在通过NAT方式访问外网
    ```
    [root@ip-172-31-25-33 ~]# docker network ls
    NETWORK ID          NAME                DRIVER              SCOPE
    6ab20f7d456c        bridge              bridge              local
    4045ea3034c2        host                host                local
    a2f91aeb97da        none                null                local
    ```
    - brige
        - ``` docker network inspect bridge``` 可以查看第一个桥接网络的详细信息
        - 也可以自行创建一个桥接网络
    - host
        - 与host主机共用网络命名空间，容器内部ip a查看到到信息和宿主机一摸一样。缺点是port容易冲突
    - none
        - 容器只有lo网卡，没有额外网卡
2. 关联2个docker容器 --link，类似于DNS，意义在容器中ping对方的容器名
    - ```docker run -it --name test1 busybox /bin/sh```
    - ```docker run -it --name test2 --link test1 busybox /bin/sh```
    - link 只是单方向的，test2只可以ping test1，反之不可
    - ctrl + p + q 离开运行中的容器
    - 场景：数据库容器，一开始并不知道数据库容器的ip地址，而且容器停止后再启动ip也会变化，所以link容器名最方便

3. 创建一个新桥接网络
    - ```docker network create -d bridge my-docker-br```, -d driver = bridge，创建桥接网络的意思
        ```
        $ brctl show
        bridge name     bridge id               STP enabled     interfaces
        docker0         8000.02422d8639ac       no              veth999db3b
                                                                veth35621dc
        br-b58348f29924         8000.024230b65dfc       no
        [node1] (local) root@192.168.0.8 ~

        $ docker network ls
        NETWORK ID          NAME                DRIVER              SCOPE
        3057b4a878ae        bridge              bridge              local
        918873e920c4        host                host                local
        b58348f29924        my-docker-br        bridge              local
        ab4435db5e07        none                null                local
        ```
    - 新建网络中创建容器test3
        ```
        $ docker run -it --name test3 --network my-docker-br busybox /bin/sh
        / # ifconfig
        eth0      Link encap:Ethernet  HWaddr 02:42:AC:13:00:02
                  inet addr:172.19.0.2  Bcast:172.19.255.255  Mask:255.255.0.0
        ```
        - ```brctl show```和 ```docker network inspect my-docker-br```均能查看到test3已经连接带新网桥
    - 将已有正在运行的容器添加到新建网络```docker network connect [OPTIONS] NETWORK CONTAINER```
        ```
        $ docker network connect my-docker-br test2
        
        $ docker exec -it test2 /bin/sh
        / # ifconfig
        eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:03
                  inet addr:172.17.0.3  Bcast:172.17.255.255  Mask:255.255.0.0

        eth1      Link encap:Ethernet  HWaddr 02:42:AC:13:00:03
                  inet addr:172.19.0.3  Bcast:172.19.255.255  Mask:255.255.0.0

        ```
        - 此时，test2可以同时访问两个网络
        - ```$ docker network inspect my-docker-br``` 和默认网桥```$ docker network inspect bridge```均能查看到test2
4. port map ```-p 80:80```





## 环境变量 -e

```
$ docker run -it --name test4 -e DAVID=david busybox /bin/sh
/ # env
HOSTNAME=793d280bc10f
DAVID=david
SHLVL=1
HOME=/root
TERM=xterm
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/
```


# EC2 redhat 安装docker

EC2 redhat instance 会自动安装podman-docker，一部分命令兼容docker
### 使用aliyun yum
https://mirrors.aliyun.com/docker-ce/linux/centos/ 选择docker-ce.repo  
- yum install docker-ce 需要忽略containerio最新版本
```
[docker-ce-stable]
name=Docker CE Stable - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-debuginfo]
name=Docker CE Stable - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-stable-source]
name=Docker CE Stable - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/stable
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-edge]
name=Docker CE Edge - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/edge
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-edge-debuginfo]
name=Docker CE Edge - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/edge
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-edge-source]
name=Docker CE Edge - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/edge
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test]
name=Docker CE Test - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-debuginfo]
name=Docker CE Test - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-test-source]
name=Docker CE Test - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/test
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly]
name=Docker CE Nightly - $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-debuginfo]
name=Docker CE Nightly - Debuginfo $basearch
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/debug-$basearch/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg

[docker-ce-nightly-source]
name=Docker CE Nightly - Sources
baseurl=https://mirrors.aliyun.com/docker-ce/linux/centos/7/source/nightly
enabled=0
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
```


### 之前的方法
1. EC2 -redhat
2. elastic ip
3. group poiicy
  - anable icmp for ping
  - enable tcp 80 for http
4. docker install
  - vim etc/yum.reop.d/redhat-rhui.repo
    > 现在的redhat-rhui.repo文件中已经没有[rhui-REGION-rhel-server-extras]这一项，需要完全手动添加。即使添加后默认安装yum install docker也是安装podman-docker
    ```diff
    [rhui-REGION-rhel-server-extras]

    - enabled=0
    + enabled=1
    ```
  - ```yum install docker -y```
5. docker container
  - ```docker pull docker.io/centos```
  - ```docker run -it -p 80:80 centos /bin/bash```
    ```
    [root@f2cbaf0c8ab1 /]# yum install -y httpd
    
    [root@f2cbaf0c8ab1 /]# apachectl
    AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.3. Set the 'ServerName' directive globally to suppress this message
    
    [root@f2cbaf0c8ab1 /]# ps -aux
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root         1  0.0  0.1  11820  1940 ?        Ss   08:32   0:00 /bin/bash
    root        88  0.0  0.3 224052  3460 ?        Ss   08:34   0:00 /usr/sbin/httpd
    apache      89  0.0  0.2 224052  2948 ?        S    08:34   0:00 /usr/sbin/httpd
    apache      90  0.0  0.2 224052  2948 ?        S    08:34   0:00 /usr/sbin/httpd
    apache      91  0.0  0.2 224052  2948 ?        S    08:34   0:00 /usr/sbin/httpd
    apache      92  0.0  0.2 224052  2948 ?        S    08:34   0:00 /usr/sbin/httpd
    apache      93  0.0  0.2 224052  2948 ?        S    08:34   0:00 /usr/sbin/httpd
    root        94  0.0  0.1  51740  1740 ?        R+   08:34   0:00 ps -aux
    ```
    - docker中启动httpd服务时，systemctl命令会报错。以下两个命令可以使用。[启动httpd服务](https://github.com/davidkorea/linux_study/blob/master/5_linux_virtualization/09_docker_net_registry.md#4-%E5%90%AF%E5%8A%A8httpd%E6%9C%8D%E5%8A%A1)
      - ```httpd```，[systemctl start httpd 等同 httpd](https://github.com/davidkorea/linux_study/blob/master/5_linux_virtualization/09_docker_net_registry.md#0-systemctl-start-httpd-%E7%AD%89%E5%90%8C-httpd)
      - ```apachectl```
      
    - 物理机可以查看到80端口
    ```
    [root@server ~]# netstat -anutp | grep 80
    tcp6       0      0 :::80        :::*      LISTEN      7467/docker-proxy-c
    ```
    - visit EC2 elastic ip ok
6. 创建docker时，不需要-d参数。直接-it进去设置。设置完成ctrl+p+q退出容器控制台，并后台继续运行
