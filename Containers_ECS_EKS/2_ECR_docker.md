







# 1. ECR
## 1.1 create a repo
![image](http://ws2.sinaimg.cn/large/006gDTsUgy1g6go5jpwp2j311y0cqjsv.jpg)

## 1.2 Command

- `$(aws ecr get-login --no-include-email --region ap-northeast-2)`
    ```
    WARNING! Using --password via the CLI is insecure. Use --password-stdin.
    WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
    Configure a credential helper to remove this warning. See
    https://docs.docker.com/engine/reference/commandline/login/#credentials-store

    Login Succeeded
    ```
    ![image](http://ws3.sinaimg.cn/large/006gDTsUgy1g6gocpuqdkj30n10oomzn.jpg)

- `aws ecr get-login --no-include-email --region ap-northeast-2`, docker临时登录AWS
    ```
    docker login -u AWS -p eyJwYXl......sb2DM0Nn0= https://2600000065.dkr.ecr.ap-northeast-2.amazonaws.com
    ```





# 2. Create Docker Image by Dockerfile
## 2.1 Dockerfile
```
FROM httpd:2.4
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    curl \
    jq \
    && rm -r /var/lib/apt/lists/*
COPY ./public-html/ /usr/local/apache2/htdocs/
ADD bootstrap.sh /bootstrap.sh
RUN chmod +x /bootstrap.sh
CMD ["/bootstrap.sh"]
```
- `--no-install-recommends`, 避免安装非必须的文件,从而减小镜像的体积
- `jq`可以对json数据进行分片、过滤、映射和转换，和sed、awk、grep等命令一样，都可以让你轻松地把玩文本
  - `cat json.txt | jq '.'`, 最简单的jq程序是表达式`'.'`，它不改变输入，但可以将其优美地输出，便于阅读和理解
  - [给力的linux命令--jq简易教程](https://www.jianshu.com/p/6de3cfdbdb0e)

## 2.2 public-html
```html
<h1>Hello world from custom Docker image</h1>

<p>This image is running on ECS, here's some information about this container and task:</p>
```
## 2.3 bootstrap.sh
```sh
#!/bin/sh
set -e

# add container metadata to index.html
echo "<pre>" >> /usr/local/apache2/htdocs/index.html
curl $ECS_CONTAINER_METADATA_URI | jq '.' >> /usr/local/apache2/htdocs/index.html
echo "</pre>" >> /usr/local/apache2/htdocs/index.html

# run httpd
httpd-foreground
```

- `set -e`, 告诉bash如果任何语句的执行结果不是true则应该退出
- `curl $ECS_CONTAINER_METADATA_URI | jq '.' `, 美化后的json
    ```json
    {
      "DockerId": "301eb66de97ff27e78ad6ff6f9abc9e1f25922b2c4e2796f907563f285a233cc",
      "Name": "httpd",
      "DockerName": "ecs-httpd-2-httpd-8ec8db93adcbbedd0a00",
      "Image": "httpd:2.4",
      "ImageID": "sha256:7d85cc3b2d8064182718e70ca9f9601a309bb7499db680e15c3231a0b350a42e",
      "Ports": [
        {
          "ContainerPort": 80,
          "Protocol": "tcp"
        }
      ],
      "Labels": {
        "com.amazonaws.ecs.cluster": "my-ecs-cluster",
        "com.amazonaws.ecs.container-name": "httpd",
        "com.amazonaws.ecs.task-arn": "arn:aws:ecs:ap-northeast-2:260099663465:task/7a9a373b-723d-4c8f-9ae7-9df62e320b40",
        "com.amazonaws.ecs.task-definition-family": "httpd",
        "com.amazonaws.ecs.task-definition-version": "2"
      },
      "DesiredStatus": "RUNNING",
      "KnownStatus": "RUNNING",
      "Limits": {
        "CPU": 0,
        "Memory": 300
      },
      "CreatedAt": "2019-08-29T06:47:30.664596462Z",
      "StartedAt": "2019-08-29T06:47:31.582913479Z",
      "Type": "NORMAL",
      "Networks": [
        {
          "NetworkMode": "bridge",
          "IPv4Addresses": [
            "172.17.0.4"
          ]
        }
      ]
    }
    ```

## 2.4 Build Docker Image
- `$(aws ecr get-login --no-include-email --region ap-northeast-2)`
- `docker build -t docker-repo .`
    ```
    Successfully built dabf103d3ea5
    Successfully tagged docker-repo:latest
    ```
- `docker tag docker-repo:latest 260099663465.dkr.ecr.ap-northeast-2.amazonaws.com/docker-repo:latest`
- `docker push 260099663465.dkr.ecr.ap-northeast-2.amazonaws.com/docker-repo:latest`
    ```
    The push refers to repository [260099663465.dkr.ecr.ap-northeast-2.amazonaws.com/docker-repo]
    c767d50684af: Preparing 
    03f98854df8f: Preparing 
    3e833f83d3fe: Preparing 
    e7dadc4bc5a7: Preparing 
    9e0ab3afff15: Preparing 
    7a0960d9b679: Waiting 
    8b16516271d6: Waiting 
    5bcb93651a74: Waiting 
    1c95c77433e8: Waiting 
    denied: User: arn:aws:sts::260099663465:assumed-role/ecsInstanceRole/i-03906de014656333d is not authorized to perform: ecr:InitiateLayerUpload on resource: arn:aws:ecr:ap-northeast-2:260099663465:repository/docker-repo
    ```
    


































