- 除了使用deplotment和service yaml文件
- 还可以只使用deployment.yaml创建，再expose出service

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

