# 消息队列的好处

- 异步
- 解耦
- 削峰

# 队列模型和主题模型

队列模型

![image-20240613095328337](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240613095328337.png)

缺点：没有广播功能，一个消息发送给多个消费者需要资源复制

订阅模型

![image-20240613095334025](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240613095334025.png)

# RocketMQ中的消息模型

![image-20240613095509847](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240613095509847.png)

topic中的一个队列只会被一个消费者消费

一般控制消费者组中的消费者个数和主题中队列个数相同

**每个消费组在每个队列上维护一个消费位置**

`RocketMQ` 通过**使用在一个 `Topic` 中配置多个队列并且每个队列维护每个消费者组的消费位置** 实现了 **主题模式/发布订阅模式** 

# [RocketMQ 的架构图](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#rocketmq-的架构图)

![image-20240613103614247](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240613103614247.png)

NameServer中存放Broker的信息（Broker路由表）

一个Topic分布在多个Broker上，一个Broker可以配置多个Topic，多对多关系

![image-20240613103916363](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240613103916363.png)

`Broker`除了做集群还可以进行主从部署，`Rocketmq` 提供了 `master/slave` 的结构，`salve` 定时从 `master` 同步数据(同步刷盘或者异步刷盘)，如果 `master` 宕机，**则 `slave` 提供消费服务，但是不能写入消息**

`NameServer`也做了集群部署，是去中心化的，`RocketMQ` 中是通过 **单个 Broker 和所有 NameServer 保持长连接** ，并且在每隔 30 秒 `Broker` 会向所有 `Nameserver` 发送心跳，心跳包含了自身的 `Topic` 配置信息，这个步骤就对应这上面的 `Routing Info`

生产者需要向 `Broker` 发送消息的时候，**需要先从 `NameServer` 获取关于 `Broker` 的路由信息**，然后通过 **轮询** 的方法去向每个队列中生产数据以达到 **负载均衡** 的效果

消费者通过 `NameServer` 获取所有 `Broker` 的路由信息后，向 `Broker` 发送 `Pull` 请求来获取消息数据。两种模式：

- 广播：一条消息给一个消费者组中的所有
- 集群：一条消息只给一个
