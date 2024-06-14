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

# [RocketMQ 功能特性](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#rocketmq-功能特性)

## [消息](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#消息)

### 普通消息

使用场景：要求数据传输通道具有可靠性，对处理时机、处理顺序没有特别要求

生命周期：

- 初始化：消息被生产者构建并完成初始化，待发送到服务端的状态。
- 待消费：消息被发送到服务端，对消费者可见，等待消费者消费的状态。
- 消费中：消息被消费者获取，并按照消费者本地的业务逻辑进行处理的过程。 此时服务端会等待消费者完成消费并提交消费结果，如果一定时间后没有收到消费者的响应，RocketMQ 会对消息进行重试处理。
- 消费提交：消费者完成消费处理，并向服务端提交消费结果，服务端标记当前消息已经被处理（包括消费成功和失败）。RocketMQ 默认支持保留所有消息，此时消息数据并不会立即被删除，只是逻辑标记已消费。消息在保存时间到期或存储空间不足被删除前，消费者仍然可以回溯消息重新消费。
- 消息删除：RocketMQ 按照消息保存机制滚动清理最早的消息数据，将消息从物理文件中删除

### [定时消息](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#定时消息)

使用场景：分布式定时调度触发、任务超时处理

定时消息仅支持在 MessageType 为 Delay 的主题内使用

生命周期：

初始化：消息被生产者构建并完成初始化，待发送到服务端的状态。

定时中：消息被发送到服务端，和普通消息不同的是，服务端不会直接构建消息索引，而是会将定时消息**单独存储在定时存储系统中**，等待定时时刻到达。

待消费：定时时刻到达后，服务端将消息重新写入普通存储引擎，【对下游消费者可见】，等待消费者消费的状态。

消费中：消息被消费者获取，并按照消费者本地的业务逻辑进行处理的过程。 此时服务端会等待消费者完成消费并提交消费结果，如果一定时间后没有收到消费者的响应，RocketMQ 会对消息进行重试处理。

消费提交：消费者完成消费处理，并向服务端提交消费结果，服务端标记当前消息已经被处理（包括消费成功和失败）。RocketMQ 默认支持保留所有消息，此时消息数据并不会立即被删除，只是逻辑标记已消费。消息在保存时间到期或存储空间不足被删除前，消费者仍然可以回溯消息重新消费。

消息删除：Apache RocketMQ 按照消息保存机制滚动清理最早的消息数据，将消息从物理文件中删除

> 先经过定时存储等待触发，时间到之后才会投递给消费者
>
> 大量同一时刻的定时消息，到达该时刻后有大量的消息需要同时被处理，给系统造成压力，导致消息延迟，影响定时精度

### [顺序消息](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#顺序消息)

顺序消息仅支持使用 MessageType 为 FIFO 的主题

和普通消息发送相比，顺序消息发送必须要设置消息组

要保证消息的顺序性需要单一生产者串行发送

单线程使用 `MessageListenerConcurrently `可以顺序消费，多线程环境下使用 `MessageListenerOrderly `才能顺序消费

### [事务消息](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#事务消息)

一种高级消息类型，支持在分布式场景下保障【消息生产和本地事务】的最终一致性

## [关于发送消息](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#关于发送消息)

### [**不建议单一进程创建大量生产者**](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#不建议单一进程创建大量生产者)

生产者和主题多对多，一个生产者可以向多个主题发送消息，不必每个主题都创建一个生产者

### [**不建议频繁创建和销毁生产者**](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#不建议频繁创建和销毁生产者)

生产者是可以重复利用的底层资源，类似数据库连接池

## [消费者分类](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#消费者分类)

### [PushConsumer](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#pushconsumer)

高度封装的消费者类型，消费消息仅仅通过消费监听器监听并返回结果

消息的获取、消费状态提交以及消费重试都通过 RocketMQ 的客户端 SDK 完成

PushConsumer 的消费监听器执行结果分为以下三种情况：

- 返回消费成功：以 Java SDK 为例，返回`ConsumeResult.SUCCESS`，表示该消息处理成功，服务端按照消费结果更新消费进度。
- 返回消费失败：以 Java SDK 为例，返回`ConsumeResult.FAILURE`，表示该消息处理失败，需要根据消费重试逻辑判断是否进行重试消费。
- 出现非预期失败：例如抛异常等行为，该结果按照消费失败处理，需要根据消费重试逻辑判断是否进行重试消费

以下方式处理消息，无法保障可靠性：

- 消息未处理完成，提前返回消费成功结果
- 在消费监听器内将消息再次分发到自定义的其他线程，消费监听器提前返回消费结果

PushConsumer 严格限制了【消息同步处理及每条消息的处理超时时间】，适用于以下场景

- 消息处理时间可预估：不确定耗时，经常超出预期，则会频繁触发重试造成大量重复
- 无异步、高级定制场景：PushConsumer 限制了消费逻辑的线程模型，由客户端 SDK 内部按最大吞吐量触发消息处理。该模型开发逻辑简单，但是不允许使用异步化和自定义处理流程

### [SimpleConsumer](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#simpleconsumer)

接口原子型的消费者类型，消息的获取、消费状态提交以及消费重试都是通过消费者业务逻辑主动发起调用完成

适用场景：

- 处理时长不可控
- 需要异步化、批量消费等高级定制场景
- 需要自定义消费速率

## [消费者分组和生产者分组](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#消费者分组和生产者分组)

### [生产者分组](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#生产者分组)

RocketMQ 服务端 5.x 版本开始，**生产者是匿名的**，无需管理生产者分组（ProducerGroup）；

对于历史版本服务端 3.x 和 4.x 版本，已经使用的生产者分组可以废弃无需再设置，且不会对当前业务产生影响。

### [消费者分组](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#消费者分组)

消费者分组是多个消费行为一致的消费者的负载均衡分组

不是具体实体，而是逻辑资源，通过消费者分组实现消费性能的水平扩展以及高可用容灾

消费者分组中的【订阅关系、投递顺序性、消费重试策略】是一致的

- 订阅关系：Apache RocketMQ 以消费者分组的粒度管理订阅关系，实现订阅关系的管理和追溯。

- 投递顺序性：Apache RocketMQ 的服务端将消息投递给消费者消费时，支持顺序投递和并发投递，投递方式在消费者分组中统一配置。

- 消费重试策略： 消费者消费消息失败时的重试策略，包括重试次数、死信队列设置等。

RocketMQ 服务端 5.x 版本：上述消费者的消费行为从关联的消费者分组中统一获取，因此，同一分组内所有消费者的消费行为必然是一致的，客户端无需关注。

RocketMQ 服务端 3.x/4.x 历史版本：上述消费逻辑由消费者客户端接口定义，因此，您需要自己在消费者客户端设置时保证同一分组下的消费者的消费行为一致。(来自官方网站)

## [如何解决顺序消费和重复消费？](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#如何解决顺序消费和重复消费)

### [顺序消费](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#顺序消费)

**`RocketMQ` 在主题上是无序的、它只有在队列层面才是保证有序** 的

- 【普通顺序】是指消费者通过 **同一个消费队列收到的消息是有顺序的** ，不同消息队列收到的消息则可能是无顺序的。普通顺序消息在 `Broker` **重启情况下不会保证消息顺序性** (短暂时间) 
- 【严格顺序】是指 消费者收到的 **所有消息** 均是有顺序的。严格顺序消息 **即使在异常情况下也会保证消息的顺序性**

> 严格顺序代价太大，Broker集群中一个不可用，都寄了，主要场景只有`binlog`同步

### 队列选择算法

- 轮询算法

  - 轮询算法就是向消息指定的 topic 所在队列中依次发送消息，保证消息均匀分布
  - 是 RocketMQ 默认队列选择算法

- 最小投递延迟算法

  - 每次消息投递的时候统计消息投递的延迟，选择队列时优先选择消息延时小的队列，导致消息分布不均匀,按照如下设置即可。

  - `producer.setSendLatencyFaultEnable(true);`

- 继承 MessageQueueSelector 实现

> 使用普通顺序的时候，使用Hash取模，自己实现相应的队列选择逻辑，保证一个订单的所有信息在一个队列中

### [特殊情况处理](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#特殊情况处理)

#### [发送异常](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#发送异常)

选择队列后会与 Broker 建立连接，通过网络请求将消息发送到 Broker 上，如果 Broker 挂了或者网络波动发送消息超时此时 RocketMQ 会进行重试。

重新选择其他 Broker 中的消息队列进行发送，默认重试两次，可以手动设置。

```java
producer.setRetryTimesWhenSendFailed(5);
```

#### [消息过大](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#消息过大)

消息超过 4k 时 RocketMQ 会将消息压缩后在发送到 Broker 上，减少网络资源的占用。

### [重复消费](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#重复消费)

幂等

根据业务来实际处理

## [RocketMQ 如何实现分布式事务？](https://javaguide.cn/high-performance/message-queue/rocketmq-questions.html#rocketmq-如何实现分布式事务)

在 `RocketMQ` 中使用的是 **事务消息加上事务反查机制** 来解决分布式事务问题的

![image-20240614153837752](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240614153837752.png)
