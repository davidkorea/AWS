
## 1. standard queue


![IMG_0022](https://user-images.githubusercontent.com/26485327/70875604-ca535c80-1ff9-11ea-9dda-6d7011e021fe.jpeg)

- 默认可见性超时
从队列接收的消息对于其他接收组件不可见的时间长度
值必须在 0 秒至 12 小时之间。

- 消息保留周期
Amazon SQS 保留消息的时间长度（如果消息未被删除）
值必须在 1 分钟至 14 天之间。

- 最大消息大小
Amazon SQS 接受的最大消息大小（字节）
值必须在 1 至 256 KB 之间。

- 交付延迟
时间长度延迟了添加至该队列的所有消息的第一次交付。
该值必须在 0 秒至 15 分钟之间。

- 接收消息等待时间
长轮询接收呼叫可以等待消息返回空响应之前可用的最长时间
值必须在 0 至 20 秒之间。


## 2. FIFO queue


![IMG_0023](https://user-images.githubusercontent.com/26485327/70876134-cc1e1f80-1ffb-11ea-8684-8a1666f1b4ec.jpeg)

- the detialed settiing are all the same as standard queue



## 3. Dead Letter queue
