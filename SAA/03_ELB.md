# 弹性负载均衡器（ELB）和健康检查
弹性负载均衡器（Elastic Load Balancing, ELB）充当了最终用户的单一触点，将访问ELB的流量分配到处于多个可用区的多个EC2实例之间。可以根据需要在负载均衡器中添加或删除EC2实例，但不中断应用程序的功能和业务。

AWS中ELB的存在，很好地替代了传统数据中心中F5负载均衡器的作用。

另外，针对ELB我们可以配置健康检查（Health Check）来保证最终用户的请求只发送到“健康”的EC2实例上，从而提高应用程序的弹性和容错性。

值得注意的一点是，在旧版本的AWS Solutions Architect Associate 考试中，对于ELB的考试内容只会涉及到传统负载均衡器（Classic Load Balancer），或者就叫做Elastic Load Balancer。

而在2018年出版的新版本考试中，会涉及到传统负载均衡器（Classic Load Balancer），应用程序负载均衡器（Application Load Balancer）和网络负载均衡器（Network Load Balancer）三种不同的负载均衡器，其中应用程序负载均衡器占比比较多。

1. 传统负载均衡器（Classic Load Balancer）
2. 应用程序负载均衡器（Application Load Balancer）
3. 网络负载均衡器（Network Load Balancer）

# 1. 传统负载均衡器（Classic Load Balancer）
![](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/images/load_balancer.png)

- Class Load Balancer可以将入向流量自动分布到多个健康的EC2实例上
- ELB是最终用户的唯一接触点
- ELB本身就是一个绝对高可用，永不宕机的分布式软件，用户不需要考虑ELB的高可用性，不需要为其设计高可用的架构设计。而且ELB不是单点故障
- ELB具有弹性，能自动对自身进行性能的提升，即可以理解为ELB能处理无穷无尽的数据请求
  - 但ELB的弹性不是立马生效的，如果应用程序在某个时间点有爆发性的流量发生（比方说淘宝双11），那么ELB是不会马上进行扩容的，扩容的过程需要一定的时间（1到7分钟）
  - 如果有可预料的爆发性流量要发生（或者需要进行压力测试），那么可以联系AWS技术支持，告诉AWS流量预计发生的开始和结束时间、预计的每秒请求数、总请求数。AWS可以对该ELB进行预热（pre-warm）从而提前达到能处理这些流量的性能大小
- ELB是阻挡来自网络攻击的第一道防线（比如DDoS攻击）
- 你不需要为ELB打补丁，或管理和维护它的操作系统
- 能分担加密和解密的工作，从而减少EC2实例的系统负担
- 能和AWS弹性伸缩（Auto Scaling）集成，从而能保证后台运行的EC2实例能满足流量的需求
- 默认情况下，ELB的流量转发规则是 TCP 侦听器使用轮询路由（Round Robin）算法，对 HTTP 和 HTTPS 侦听器使用最少未完成请求路由算法。
- ELB只在一个特定的AWS区域中工作，不能跨区域（Region），但可以跨可用区（AZs）
- 基于ELB在所处应用架构中的位置不同，可以分两个类型ELB
  - Internet Load Balancer – 是面向公网的负载均衡器，能接受来自Internet用户的连接请求
  - Internal Load Balancer – 是面向AWS私有网段的负载均衡器，一般仅服务于AWS内部的资源。典型的使用案例是放置在前端服务器和后端服务器之间
## 1.1 DNS解析
- ELB会以DNS (Domain Name System)的形式显示在AWS管理控制台，并且会动态解析不同公网IP地址，我们在使用ELB时要尽量用DNS来对它进行访问，而不是IP地址
- 在ELB进行弹性扩容的时候，它的DNS记录会被更新，DNS会解析到新的IP地址上（因此这也是上面所说的我们要尽量用DNS名来访问ELB）
- DNS记录的TTL时间是60秒
- 建议客户端（程序）每60秒更新DNS查找记录，以获取最新的ELB地址和最好的ELB性能
## 1.2 健康检查（Health Check)
ELB在每一个**健康检查间隔（HealthCheck Interval）**都会向所有已注册的实例发送基于**Ping、端口或者（网页）路径**的检查数据包，并且在**响应超时（Response Timeout）** 这个时间内等待实例的回复。如果连续 **没有** 得到回复的次数超过定义的**不健康阈值（Unhealthy Threshold）**，那么这个实例会被标记为**OutofService**。如果在连续得到实例回复的次数超过了**健康阈值（Healthy Threshold）**的话，那么这个实例会被重新标记为**Inservice**状态。

- ELB会对所有注册到这个ELB上的EC2实例进行健康检查，无论目前的健康状态如何
- ELB的监控状态分别为InService（表示健康）或者OutofService（表示不健康）
## 1.3 监听器（Listeners）
- Listeners可以用来监听用户对ELB发起的请求，以及ELB和后台EC2实例之间的请求
- Listeners可以定义监听的协议和端口
- Listeners支持**HTTP, HTTPS, SSL, TCP协议**
## 1.4 连接耗尽（Connection Draining）
默认情况下，一个已注册在ELB的EC2实例取消了注册或者进入OutofService状态，那么ELB会马上切断这个实例正在进行的连接。

为了保证Classic Load Balancer中当有实例变成不健康的状态（OutofService）或者正在取消注册，而**该实例上已经建立的连接不受影响**， 请启用Connection Draining功能。它能**保证该不健康的实例在处理完所有已有的连接请求之后，才真正地从ELB内去除，接着ELB不会再转发请求给这个实例**。

Connection Draining的可设置时间限制范围是1~3600秒（默认为300秒）。当达到这个最大时限时，不管当前实例是否处理完请求，ELB都会强制关闭与这个实例的连接。

## 1.5 粘性会话/会话关联（Sticky Sessions/Session Affinity）
默认情况下，Classic Load Balancer会将每一个用户请求转发到负载最小的已注册实例上。但是如果启用Sticky Sessions /Session Affinity，则在**会话期间ELB会将来自某个用户的所有请求都转发到同一个实例上**。

# 2. 应用程序负载均衡器（Application Load Balancer）

![](https://docs.aws.amazon.com/zh_cn/elasticloadbalancing/latest/application/images/component_architecture.png)





