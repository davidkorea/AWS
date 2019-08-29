

# 1. Create Docker Image by Dockerfile
## 1.1 Dockerfile
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

## 1.2 public-html
```html
<h1>Hello world from custom Docker image</h1>

<p>This image is running on ECS, here's some information about this container and task:</p>
```
## 1.3 bootstrap.sh
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
