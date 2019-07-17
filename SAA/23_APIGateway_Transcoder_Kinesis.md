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


![](https://i.loli.net/2019/07/17/5d2e7bce35a8881341.png)
![](https://i.loli.net/2019/07/17/5d2e7bd0d496394561.png)
![](https://i.loli.net/2019/07/17/5d2e7bd3a4b8783303.png)
![](https://i.loli.net/2019/07/17/5d2e7bd6bc9dc50067.png)
![](https://i.loli.net/2019/07/17/5d2e7bd90270786727.png)
![](https://i.loli.net/2019/07/17/5d2e7bdb098b333474.png)
![](https://i.loli.net/2019/07/17/5d2e7bdd2747695762.png)
![](https://i.loli.net/2019/07/17/5d2e7be0157a585846.png)




# 2. Elastic Transcoder简介
在助理级的AWS考试中，会有少量的问题是关于Elastic Transcoder的，但内容不会很多，也不会很深。因此本章内容只会做一些基本的讲解。

Amazon Elastic Transcoder是一种在线媒体转码的工具，使用它我们可以很容易地将我们的视频从源格式转换到其他的格式和分辨率，以便在手机、平板、PC等设备上播放。

一般来说，我们会将需要转码的媒体文件放在AWS S3的存储桶上，创建相应的管道和任务将文件转码为特定的格式，最后将文件输出到另一个S3的存储桶上面去。

我们也可以使用一些预设的模板来转换我们的媒体格式。

另外，我们也可以配合Lambda函数，在有新的文件上传到S3后触发函数代码，执行Elastic Transcoder并自动进行媒体文件的转码。

# 3. Kinesis简介
Amazon Kinesis可以让你轻松收集、处理和分析实时流数据。利用Amazon Kinesis，你可以在收到数据的同时对数据进行处理和分析，无需等到数据全部收集完成才进行处理。

## 3.1 数据流
数据流是从成千上万的数据源上持续产生的数据，并且这些数据都很小（KB级别），它们可能是：
- 电商网站上的订单信息（比如京东，淘宝）
- 股票信息
- 游戏信息
- 社交网络信息（微信/微博的信息流）
- 地理位置信息（滴滴）
- 物联网数据

![](https://i.loli.net/2019/07/17/5d2e7f549bf2354193.png)

## 3.2 Kinesis服务
Kinesis目前有不同的功能服务，我们需要了解每一个服务有什么不同。这些服务分别是：

- Kinesis Data Streams (Kinesis Streams)：使用自定义的应用程序分析数据流
- Kinesis Video Streams：捕获、处理并存储视频流用于分析和机器学习（Machine Learning）
- Kinesis Data Firehose：将数据加载到AWS数据存储上
- Kinesis Data Analytics：使用SQL分析数据流
### 3.2.1 Kinesis Data Streams
- Amazon Kinesis Data Streams可以实时收集和处理大型数据流，这些数据会被处理并且发送到多种AWS服务中去，也可以生成报警、动态更改定价和广告战略等。
- 如图所示，创建者（Producer）会持续将数据推送到Kinesis Data Streams中，这些创建者包括了EC2实例、用户的PC终端、移动终端，服务器等。
- Kinesis Data Streams由一组分片（Shards）组成，每个shards都有一系列的数据记录，每一个数据记录都有一个分配好的序列号。
- 数据记录在添加到流之后会保存一定的时间，这个保留周期（Retention Period）默认是24小时，但可以手动设置为最多7天。
- **使用者（Comsumer）会实时地对Kinesis Streams里的内容进行处理，并将最终结果推送到AWS服务，例如Amazon S3，DynamoDB，Redshift，Amazon EMR或者Kinesis Firehose。**

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/aws-kinesisdata-streams.png)

> **You might be doing sentiment analysis on Twitter on a particular hashtag. Do people feel good about this particular hashtag or do people feel bad about a particular hashtag.**

![](https://i.loli.net/2019/07/17/5d2e7f848031534584.png)
![](https://i.loli.net/2019/07/17/5d2e7f90a579d82675.png)

### 3.2.2 Kinesis Video Streams
- Kinesis Video Streams主要用来进行实时的视频处理，或者批量进行视频分析。
- Kinesis Video Streams可以捕获来自多种设备类型的视频流数据（比如智能手机、网络摄像头、车载摄像头、无人机等）。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/aws-kinesis-video-streams.png)


### 3.2.3 Kinesis Data Firehose
Kinesis Data Firehose可以让我们的**实时数据流传输到我们定义的目标**，包括Amazon S3，Amazon Redshift，Amazon Elasticsearch Service (ES)和Splunk。
如下图所示，通过Kinesis Firehose，我们可以将数据流经过转换之后传输到S3存储桶上去，并且另外将源数据备份一份到另一个S3存储桶。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/fh-flow-s3.png)

![](https://i.loli.net/2019/07/17/5d2e809c0b35594076.png)

### 3.2.4 Kinesis Data Analytics
使用Kinesis Data Analytics，我们可以使用标准的SQL语句来处理和分析我们的数据流。这个服务可以让我们使用强大的SQL代码来做实时的数据流分析、创建实时的参数。

![](https://i.loli.net/2019/07/17/5d2e809e339b718586.png)
