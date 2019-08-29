

# 1. Create Docker Image by Dockerfile
## 1. Dockerfile
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



- set -e,这句语句告诉bash如果任何语句的执行结果不是true则应该退出
