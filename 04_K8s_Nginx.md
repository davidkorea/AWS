- 除了使用deplotment和service yaml文件
- 还可以只使用deployment.yaml创建，再expose出service
## Deployment
```
[root@server162 ~]# vim nginx-deployment.yaml 
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: nginx
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: nginx
    spec:
      containers:
      - name: nginx
        image:  docker.io/nginx:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
          protocol: TCP
```
## Service
```
[root@server162 ~]# vim nginx-svc.yaml 
kind: Service
apiVersion: v1
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  type: NodePort      # NodePort类型可以外网访问，云端需要使用LoadBalancer
  ports:
  - protocol: TCP
    nodePort: 31001   # 用户可以通过node节点上这个端口访问nginx，物理机master的公网上的端口，不指定时，k8s会随机分配
    targetPort: 80    # 指定 nginx docker 容器的端口
    port: 80          # 指定 pod 端口
  selector:
    name: nginx
```
```
[root@seoul ~]# kubectl
  expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
```
```
kubectl expose (-f FILENAME | TYPE NAME) [--port=port]
[--protocol=TCP|UDP|SCTP] [--target-port=number-or-name] [--name=name]
[--external-ip=external-ip-of-service] [--type=type] [options]

      --type='': Type for this service: ClusterIP, NodePort, LoadBalancer, or ExternalName. Default is 'ClusterIP'.
```
- ClusterIP 服务是 Kubernetes 的默认服务。它给你一个集群内的服务，集群内的其它应用都可以访问该服务。集群外部无法访问它。
- NodePort 服务是引导外部流量到你的服务的最原始方式。NodePort，正如这个名字所示，在所有节点（虚拟机）上开放一个特定端口，任何发送到该端口的流量都被转发到对应服务。如果你不指定这个端口，系统将选择一个随机端口。
- LoadBalancer 服务是暴露服务到 internet 的标准方式。在 GKE 上，这种方式会启动一个 Network Load Balancer[2]，它将给你一个单独的 IP 地址，转发所有流量到你的服务。

[Kubernetes的三种外部访问方式：NodePort、LoadBalancer和Ingress](https://www.cnblogs.com/devilwind/p/8891636.html)

如果不使用yaml文件，则需要以下命令来创建
- ```kubectl expose pod [pod-name] --type=LoadBalancer```


