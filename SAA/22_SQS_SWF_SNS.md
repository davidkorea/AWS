# 1. SQS (Simple Queue Service)简介
Amazon Simple Queue Service (SQS)是一种完全托管的消息队列服务，可以让你分离和扩展微服务、分布式系统和无服务应用程序。

## 1.1消息队列
还是举一个电商的例子，一个用户在电商网站下单后付款后，应用服务器马上查询/更新数据库，连接支付网关并查询支付状态，通知短信/邮件网关发送相关短信/邮件，更新库存系统，更新物流系统……最后返回信息给用户，“您的下单已成功”。

但是如果网站的访问数很大，或者正值促销活动（比如淘宝双11，京东618）呢？

这个时候每一个流程都是一个瓶颈，一旦某一个地方达到了瓶颈或者出现故障，又或者用户下单的时间比程序处理订单的时间还要久的情况下，都会让用户得不到成功下单的结果，或者得到结果的时间非常长，导致用户体验不好。

这个时候，我们就要考虑到应用程序的**解耦（decouple）**

我们可以引入消息队列，让不同的应用程序之间打断强连接的关系，互不干扰。

应用服务器在接收到用户付款的订单之后，就把相关的信息丢到消息队列，并且返回用户“您的下单已成功，请稍后查看详细订单状态”。

而支付网关、短信/邮件网关、库存系统、物流系统等等可以到消息队列里面拉取信息，并且进行相关的数据更新和操作。

这些操作可能不需要是实时的，但是至少能保证这些队列里的信息最终都会被执行。比如下单后我不一定马上能收到短信/邮件的通知，我可能5分钟/10分钟之后才收到这些信息通知，但这个并不影响正常的业务。

这样子，消息队列就起到了连接上层业务和下层业务的作用。

Amazon SQS相当于提供了一个分布式、高可用、高性能的消息队列服务。

![](https://i.loli.net/2019/07/16/5d2d8bacb026386043.png)

## 1.2 SQS特点
SQS有两种不同类型的队列，它们分别是：

1. 标准队列（Standard Queue）
2. FIFO队列（先进先出队列）
### 1.2.1 标准队列
标准队列拥有无限的吞吐量，所有消息都会至少传递一次，并且它会尽最大努力进行排序。标准队列是默认的队列类型。
![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/sqs-what-is-sqs-standard-queue-diagram.png)


### 1.2.2 FIFO队列
- FIFO (First-in-first-out)队列在不使用批处理的情况下，最多支持300TPS（每秒300个发送、接受或删除操作）。
- 在队列中的消息都只会不多不少地被处理一次。
- FIFO队列严格保持消息的发送和接收顺序。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/sqs-what-is-sqs-fifo-queue-diagram.png)

### 1.2.3 更多关于标准队列和FIFO队列的区别，可以查看[我需要哪种类型的队列？](https://docs.aws.amazon.com/zh_cn/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html#sqs-queue-types)
![](https://i.loli.net/2019/06/19/5d09913ad4b2766995.png)

## 1.3 SQS的其他特点
- SQS是靠应用程序去拉取的，而不能主动推送给应用程序，推送服务我们使用SNS（Simple Notification Service）
- 消息会以256 KB的大小存放
- 消息会在队列中保存1分钟~14天，默认时间是4天
- **可见性超时（Visibility Timeout）**
  - 即当SQS队列收到新的消息并且被拉取走进行处理时，会触发Visibility Timeout的时间。这个消息不会被删除，而是会被设置为不可见，用来防止该消息在处理的过程中再一次被拉取
  - 当这个消息被处理完成后，这个消息会在SQS中被删除，表示这个任务已经处理完毕
  - 如果这个**消息在Visibility Timeout时间结束之后还没有被处理完，则这个消息会设置为可见状态，等待另一个程序来进行处理**
  - 只应于标准队列吧，因为在FIFO队列中，在队列中的消息都只会不多不少地被处理一次
  - 因此同一个消息可能会被处理两次（或以上）
  - 这个超时时间最大可以设置为12小时
  - 标准SQS队列保证了每一个在队列内的消息都至少会被处理一次
- 长轮询（Long Polling）
  - 默认情况下，Amazon SQS使用短轮训（Short Polling），即应用程序每次去查询SQS队列，SQS都会做回应（哪怕队列一直是空的）
  - 使用了长轮训，应用程序每次去查询SQS队列，SQS队列不会马上做回应。而是等到队列里有消息可处理时，或者等到设定的超时时间再做出回应。
  - **长轮询可以一定程度减少SQS的花销**


# 2. SNS (Simple Notification Service)简介
SNS (Simple Notification Service) 是一种完全托管的发布/订阅消息收发和移动通知服务，用于协调向订阅终端节点和客户端的消息分发。

和SQS (Simple Queue Service)一样，SNS也可以轻松分离和扩展微服务，分布式系统和无服务应用程序，对程序进行解耦。

我们可以使用SNS将消息推送到SQS消息队列中、AWS Lambda函数或者HTTP终端节点上。

SNS通知还可以发送推送通知到IOS，安卓，Windows和基于百度的设备，也可以通过电子邮箱或者SMS短信的形式发送到各种不同类型的设备上。
![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/sns-how-works.png)

## 2.1 SNS的一些特点
- SNS是实时的推送服务（Push），有别于SQS的拉取服务（Pull/Poll）
- 拥有简单的API，可以和其他应用程序兼容
- 可以通过多种不同的传输协议进行集成
- 便宜、用多少付费多少的服务模型
- 在AWS管理控制台上就可以进行简单的操作
## 2.2 SNS能推送的目标
- HTTP
- HTTPS
- Email
- Email-JSON
- SQS
- Application
- Lambda


# 3. SWF (Simple Workflow Service)简介
**有 人 参与**

Amazon Simple Workflow Service (Amazon SWF) 提供了给应用程序异步、分布式处理的流程工具。

SWF可以用在媒体处理、网站应用程序后端、商业流程、数据分析和一系列定义好的任务上。

举个例子，下图表明了一个电商网站的工作流程，其中涉及了程序执行的过程和人工执行的过程（第三步）。

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/swf-overview-workflow.png)

当用户在电商网站上下单后，即启动了该流程，该流程包含了4个任务（tasks）：

- SWF验证用户订单信息
- 如果订单有效，则进行信用卡付款流程
- 如果付款完毕，则进行人工发货
- 如果发货完成，则保存订单信息到数据库，并结束流程

在这个流程中，每一个任务都是按顺序执行的，只有当上一个任务成功完成后才能执行下一个任务。

SWF除了支持顺序执行的流程之外，也支持并行处理的流程，即一个任务的完成可以触发多个任务同时执行。

## 3.1 基本的SWF概念
- SWF发起者（Starter）
  可以激活一个工作流的应用程序，可能是电商网站上下单的行为，或者是在收集APP上点击某个按钮
- SWF决策者（ Decider）
  SWF Decider决定了任务之间的协调，处理的顺序，并发性和任务的逻辑控制
- SWF参与者（Worker）
  SWF Worker可以在SWF中或许新的任务，处理任务，并且返回结果
- SWF域（Domains）
  域包含了工作流的所有组成部分，比如工作流类型和活动类型
  
SWF决策者和参与者可以是运行在AWS上的EC2实例或者其他计算资源，SWF只是保存不同的任务，把这些任务分配给worker，并且监控他们的任务处理进展。

## 3.2 SWF和SQS的区别
- SWF是面向任务的；SQS是面向消息的；
- **SWF保证了每一个任务都只执行一次而不会重复**；标准的SQS消息可能会被处理多次
- SWF保证了程序内所有任务都正常被处理，并且追踪工作流；而SQS只能在应用程序的层面追踪工作流
- SWF内的任务最长可以保存1年；SQS内的消息最长只能保存14天
