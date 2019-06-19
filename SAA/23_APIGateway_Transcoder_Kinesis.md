# 1. API Gateway简介
Amazon API Gateway可以让开发人员创建、发布、维护、监控和保护任何规模的API。你可以创建能够访问 AWS、其他 Web 服务以及存储在 AWS 云中的数据的API。

API Gateway没有最低使用成本，我们用多少服务内容就花费多少。

比如在最新的A Cloud Guru的serverless 会议上面提到了，他们整个网站都是基于API Gateway和Lambda的，并没有任何计算服务器（EC2，ECS等），永远不用担心性能和扩容的问题。并且他们每个月的花销只是580美金！

理解什么是API Gateway，它能用来做什么
- API Gateway可以缓存内容，从而更快地将一些常用内容发送给用户
- API Gateway是一种低成本的无服务（serverless）方案，而且它可以自动弹性伸缩（类似ELB，NAT网关）
- 可以对API Gateway进行节流，以防止恶意攻击
- 可以将API Gateway的日志放到CloudWatch中
- 如果你使用JavaScript/AJAX来跨域访问资源，那么你需要保证在API Gateway上已经开启了CORS (Corss-Origin Resource Sharing)功能
- 如果没有开启CORS功能，在使用API Gateway做跨域访问的时候，可能会出现错误 “Origin policy cannot be read at the remote resource?”
- 我们在S3的课程中也介绍过CORS的功能，可以参见S3的课程
