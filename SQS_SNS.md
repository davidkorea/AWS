# SQS VS SNS


1. SQS：同一条消息，在一个时间段，只能被一个·consumer（EC2）处理

2. SNS: 同一条信息，可以被同一topik的所有订阅者，同时执行


![IMG_0033](https://user-images.githubusercontent.com/26485327/70961584-93e31380-20c5-11ea-8d17-2fdcabc7894e.jpeg)

# SQS

## 1. standard queue


![IMG_0022](https://user-images.githubusercontent.com/26485327/70875604-ca535c80-1ff9-11ea-9dda-6d7011e021fe.jpeg)

- 默认可见性超时 VIsibility, 从队列接收的消息对于其他接收组件不可见的时间长度, 值必须在 0 秒至 12 小时之间, 默认30sec
  - 可以针对 每条消息 设置可见超时，而不是针对整个队列设置
  - 如果EC2处理一条消息用时较长，则需要相应增加可见性超时 时间，以防止当前消息正在被处理中，由于超时，导致其他EC2又可以看到，导致相同消息被多次处理
  - 如果需要超过12h，则应该考虑使用 AWS Step Functions
- 消息保留周期
Amazon SQS 保留消息的时间长度（如果消息未被删除）
值必须在 1 分钟至 14 天之间。

- 最大消息大小
Amazon SQS 接受的最大消息大小（字节）
值必须在 1 至 256 KB 之间。

- 交付延迟
时间长度延迟了添加至该队列的所有消息的第一次交付。
该值必须在 0 秒至 15 分钟之间。

- 接收消息等待时间ReceiveMessage，长轮询接收呼叫可以等待消息返回空响应之前可用的最长时间，值必须在 0 至 20 秒之间，默认0sec
  - 如果开启longpolling，则范围是1-20sec，默认20sec


console and  use CLI or SDK can send messages to queue
- 一条消息包涵 正文 + 属性
- 每条消息必须有正文，可以没有属性，属性不随正文一起发送
- 可以设置应用仅处理 属性中的内容，而不处理正文内容
- 每条消息最多10个属性

> Amazon SQS 消息属性：可以 使用消息属性在消息中包括结构化的元数据 (例如时间戳、地理空间数据、签名和标识符)。。每条消息最多可以包含 10 个属性。消息属性是可选的，并独立于消息正文 (不过会随之一起发送)。使用者可以使用消息属性以特定方式处理消息，而无需先处理消息正文。有关使用 AWS 管理控制台或AWS SDK for Java发送带有属性消息的信息，https://docs.aws.amazon.com/zh_cn/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-message-attributes.html

![IMG_0025](https://user-images.githubusercontent.com/26485327/70877427-beb76400-2000-11ea-8a07-9f473e4acaee.jpeg)
![IMG_0026](https://user-images.githubusercontent.com/26485327/70877428-c1b25480-2000-11ea-8359-7c49e73e2535.jpeg)
![IMG_0027](https://user-images.githubusercontent.com/26485327/70877433-c4ad4500-2000-11ea-8d27-1319c1d5e0a2.jpeg)





## 2. FIFO queue


![IMG_0023](https://user-images.githubusercontent.com/26485327/70876134-cc1e1f80-1ffb-11ea-8684-8a1666f1b4ec.jpeg)

- the detialed settiing are all the same as standard queue



## 3. Dead Letter Queue (DLQ)

- origin queue 失败了的消息会被发送到死信duilie
- 死信队列和原队列在统一region
- standard queue的死信必须是standard，FIFO queue的死信必须是FIFO
- 死信队列的 retention时间要大于原队列，因为发送到死信队列的消息的时间，依然按照原队列的时间
  - 对过原队列中已经等待了1天未被成功处理，发送到死信队列，那么在死信队列中也相当于保存了1天

- `Maximum Receives`, 如果数值太小，而且又在conosle查看了几次，可能还没有被处理，就发送到死信队列
![IMG_0029](https://user-images.githubusercontent.com/26485327/70958083-7446ed80-20bb-11ea-8eff-edfcec742108.jpeg)


## 4. shared queue

- messages can not be shared between queues in different region
- queue owner is responisble for the cost
- CLI and SDk can enbale shared queue



# SNS
## 1. Typical SNS
- supported endpoints
  - AWS Lambda
  - Amazon SQS
  - HTTP and HTTPS
  - Email
  - SMS
  - Mobile PushRecords

![IMG_0030](https://user-images.githubusercontent.com/26485327/70958696-727e2980-20bd-11ea-8443-bd312890cd36.jpeg)

- transport protocols
  - HTTP， HTTPS
  - email，email-json
  - Amazon SQS
  - SMS
![IMG_0044](https://user-images.githubusercontent.com/26485327/70978309-e555c780-20f2-11ea-8afc-8ed5b8f0c8aa.jpeg)


### Demo SMS
- Tpoic
- Subcriber
![IMG_0042](https://user-images.githubusercontent.com/26485327/70976016-6c547100-20ee-11ea-98e9-35e4b5b74ed0.jpeg)

![IMG_0043](https://user-images.githubusercontent.com/26485327/70977532-7d52b180-20f1-11ea-993a-87abc3ab71bd.jpeg)


![IMG_0045](https://user-images.githubusercontent.com/26485327/70978313-e71f8b00-20f2-11ea-966c-577f30266720.jpeg)
![IMG_0873](https://user-images.githubusercontent.com/26485327/70978377-05858680-20f3-11ea-8331-3a572103612f.jpeg)


## 2. Mobile app SNS
- Mobile Push Notifications 移动设备
  - send notifications to the apps installed on the one device
![IMG_0031](https://user-images.githubusercontent.com/26485327/70959370-6f843880-20bf-11ea-97c9-cb206e8c4b19.jpeg)

 
  - push notification services
    - Amazon Device Messaging (ADM)
    - Apple Push Notification Service (APNS) for both iOS and macOS 
    - Baidu Cloud Push (Baidu)
    - Google Cloud Messaging for Android (GCM)
    - Microsoft Push Notification Service for Windows Phone (MPNS) 
    - Windows Push Notification Services (WNS)
  
  - mobile endpoint as a subscriber to an Amazon SNS topic. The mobile endpoint communicates with push notification services, whereas the other endpoints do not.
![IMG_0032](https://user-images.githubusercontent.com/26485327/70961438-26cf7e00-20c5-11ea-879d-cf58871cd3be.jpeg)

















