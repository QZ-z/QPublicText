# [Redis 基础](https://javaguide.cn/database/redis/redis-questions-01.html#redis-基础)

## [什么是 Redis？](https://javaguide.cn/database/redis/redis-questions-01.html#什么是-redis)

[Redis](https://redis.io/) （**RE**mote **DI**ctionary **S**erver）是一个基于 C 语言开发的开源 NoSQL 数据库（BSD 许可）

数据保存在内存中，存储的是KV键值对

## [Redis 为什么这么快？](#redis-为什么这么快)

Redis 内部做了非常多的性能优化，比较重要的有下面 3 点：

1. Redis 基于内存，内存的访问速度比磁盘快很多；
2. Redis 基于 Reactor 模式设计开发了一套高效的事件处理模型，主要是单线程事件循环和 IO 多路复用（Redis 线程模式后面会详细介绍到）；
3. Redis 内置了多种优化过后的数据类型/结构实现，性能非常高。
4. Redis 通信协议实现简单且解析高效。

> 内存、事件处理模型、高效数据结构、通信协议高效
>
> 为什么不用redis做主数据库：内存成本高、持久化仍然有数据丢失的风险

## [分布式缓存常见的技术选型方案有哪些？](https://javaguide.cn/database/redis/redis-questions-01.html#分布式缓存常见的技术选型方案有哪些)

之前Memcached，后来就是redis了

目前，比较业界认可的 Redis 替代品还是下面这两个开源分布式缓存（都是通过碰瓷 Redis 火的）：

- [Dragonfly](https://github.com/dragonflydb/dragonfly)：一种针对现代应用程序负荷需求而构建的内存数据库，完全兼容 Redis 和 Memcached 的 API，迁移时无需修改任何代码，号称全世界最快的内存数据库。
- [KeyDB](https://github.com/Snapchat/KeyDB)： Redis 的一个高性能分支，专注于多线程、内存效率和高吞吐量。

## [Memchched和redis的对比]()

**共同点**：

1. 都是基于内存的数据库，一般都用来当做缓存使用。
2. 都有过期策略。
3. 两者的性能都非常高。

**区别**：

1. **数据类型**：

   - Redis 支持更丰富的数据类型（支持更复杂的应用场景）。Redis 不仅仅支持简单的 k/v 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。
   - Memcached 只支持最简单的 k/v 数据类型。

2. **数据持久化**：

   - Redis 支持数据的持久化

   - Memcached不支持

3. **集群模式支持**：

   - Memcached 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；

   - 但是 Redis 自 3.0 版本起是原生支持集群模式的。

4. **线程模型**：

   - Memcached 是多线程，非阻塞 IO 复用的网络模型；

   - Redis 使用单线程的多路 IO 复用模型。 （Redis 6.0 针对网络数据的读写引入了多线程）

5. **特性支持**：

   - Redis 支持发布订阅模型、Lua 脚本、事务等功能，而 Memcached 不支持。

   - 并且，Redis 支持更多的编程语言。

6. **过期数据删除**：

   - Memcached 过期数据的删除策略只用了惰性删除

   - 而 Redis 同时使用了惰性删除与定期删除。

## [为什么要用 Redis？](https://javaguide.cn/database/redis/redis-questions-01.html#为什么要用-redis)

**1、访问速度更快**

**2、高并发**

一般像 MySQL 这类的数据库的 QPS 大概都在 4k 左右（4 核 8g） ，但是使用 Redis 缓存之后很容易达到 5w+，甚至能达到 10w+（就单机 Redis 的情况，Redis 集群的话会更高）。

> QPS（Query Per Second）：服务器每秒可以执行的查询次数；

**3、功能全面**

Redis 除了可以用作缓存之外，还可以用于分布式锁、限流、消息队列、延时队列等场景，功能强大

## [常见的缓存读写策略有哪些？](https://javaguide.cn/database/redis/redis-questions-01.html#常见的缓存读写策略有哪些)

### [Cache Aside Pattern（旁路缓存模式）](#cache-aside-pattern-旁路缓存模式)

**Cache Aside Pattern 是我们平时使用比较多的一个缓存读写模式，比较适合读请求比较多的场景。**

Cache Aside Pattern 中服务端需要同时维系 db 和 cache，并且是以 db 的结果为准。

**写**：

- 先更新 db
- 然后直接删除 cache 。

**读** :

- 从 cache 中读取数据，读取到就直接返回
- cache 中读取不到的话，就从 db 中读取数据返回
- 再把数据放到 cache 中。

问：**在写数据的过程中，可以先删除 cache ，后更新 db 么**

不可以，会造成数据库和缓存不一致

问：**在写数据的过程中，先更新 db，后删除 cache 就没有问题了么？**

理论上来说还是可能会出现数据不一致性的问题，不过概率非常小，因为缓存的写入速度是比数据库的写入速度快很多。

![1653323595206](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/1653323595206.png)

上图的两种情况都会造成数据不一致，但是右侧的概率小了很多。将缓存设置过期时间后，数据不一致将进一步缓解。

**缺陷 1：首次请求数据一定不在 cache 的问题**

解决办法：可以将热点数据可以提前放入 cache 中。

**缺陷 2：写操作比较频繁的话导致 cache 中的数据会被频繁被删除，这样会影响缓存命中率 。**

解决办法：

- 数据库和缓存数据强一致场景：更新 db 的时候同样更新 cache，不过我们需要加一个锁/分布式锁来保证更新 cache 的时候不存在线程安全问题。
- 可以短暂地允许数据库和缓存数据不一致的场景：更新 db 的时候同样更新 cache，但是给缓存加一个比较短的过期时间，这样的话就可以保证即使数据不一致的话影响也比较小。

### [Read/Write Through Pattern（读写穿透）](https://javaguide.cn/database/redis/3-commonly-used-cache-read-and-write-strategies.html#read-write-through-pattern-读写穿透)

Read/Write Through Pattern 中服务端把 cache 视为主要数据存储，从中读取数据并将数据写入其中。

cache 服务负责将此数据读取和写入 db，从而减轻了应用程序的职责。

**写（Write Through）：**

- 先查 cache，cache 中不存在，直接更新 db。
- cache 中存在，则先更新 cache，然后 cache 服务自己更新 db（**同步更新 cache 和 db**）。

![image-20240505215109594](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240505215109594.png)

**读(Read Through)：**

- 从 cache 中读取数据，读取到就直接返回 。
- 读取不到的话，先从 db 加载，写入到 cache 后返回响应。

![image-20240505215118191](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240505215118191.png)

Read-Through Pattern 实际只是在 Cache-Aside Pattern 之上进行了封装。

在 Cache-Aside Pattern 下，发生读请求的时候，如果 cache 中不存在对应的数据，是由客户端自己负责把数据写入 cache，而 Read Through Pattern 则是 cache 服务自己来写入缓存的，这对客户端是透明的。

和 Cache Aside Pattern 一样， Read-Through Pattern 也有首次请求数据一定不在 cache 的问题，对于热点数据可以提前放入缓存中。

### [Write Behind Pattern（异步缓存写入）](#write-behind-pattern-异步缓存写入)

Write Behind Pattern 和 Read/Write Through Pattern 很相似，两者都是由 cache 服务来负责 cache 和 db 的读写。

但是，两个又有很大的不同：

- **Read/Write Through 是同步更新 cache 和 db**
- **Write Behind 则是只更新缓存，不直接更新 db，而是改为异步批量的方式来更新 db。**

缺陷：cache 数据可能还没异步更新 db 的话，cache 服务就挂掉了。

这种策略在我们平时开发过程中也非常非常少见，但是不代表它的应用场景少，比如消息队列中消息的异步写入磁盘、MySQL 的 Innodb Buffer Pool 机制都用到了这种策略。

Write Behind Pattern 下 db 的写性能非常高，非常适合一些数据经常变化又对数据一致性要求没那么高的场景，比如浏览量、点赞量。

## [什么是 Redis Module？有什么用？](#什么是-redis-module-有什么用)

Redis 从 4.0 版本开始，支持通过 Module 来扩展其功能以满足特殊的需求。这些 Module 以动态链接库（so 文件）的形式被加载到 Redis 中

目前，被 Redis 官方推荐的 Module 有：

- [RediSearch](https://github.com/RediSearch/RediSearch)：用于实现搜索引擎的模块。
- [RedisJSON](https://github.com/RedisJSON/RedisJSON)：用于处理 JSON 数据的模块。
- [RedisGraph](https://github.com/RedisGraph/RedisGraph)：用于实现图形数据库的模块。
- [RedisTimeSeries](https://github.com/RedisTimeSeries/RedisTimeSeries)：用于处理时间序列数据的模块。
- [RedisBloom](https://github.com/RedisBloom/RedisBloom)：用于实现布隆过滤器的模块。
- [RedisAI](https://github.com/RedisAI/RedisAI)：用于执行深度学习/机器学习模型并管理其数据的模块。
- [RedisCell](https://github.com/brandur/redis-cell)：用于实现分布式限流的模块。

# [Redis 应用](https://javaguide.cn/database/redis/redis-questions-01.html#redis-应用)

## [Redis 除了做缓存，还能做什么？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-除了做缓存-还能做什么)

**分布式锁**：通过 Redis 来做分布式锁是一种比较常见的方式。通常情况下，我们都是基于 Redisson 来实现分布式锁。关于 Redis 实现分布式锁的详细介绍，可以看我写的这篇文章：[分布式锁详解](https://javaguide.cn/distributed-system/distributed-lock.html) 。

**限流**：一般是通过 Redis + Lua 脚本的方式来实现限流。如果不想自己写 Lua 脚本的话，也可以直接利用 Redisson 中的 `RRateLimiter` 来实现分布式限流，其底层实现就是基于 Lua 代码+令牌桶算法。

**消息队列**：Redis 自带的 List 数据结构可以作为一个简单的队列使用。Redis 5.0 中增加的 Stream 类型的数据结构更加适合用来做消息队列。它比较类似于 Kafka，有主题和消费组的概念，支持消息持久化以及 ACK 机制。

**延时队列**：Redisson 内置了延时队列（基于 Sorted Set 实现的）。

**分布式 Session** ：利用 String 或者 Hash 数据类型保存 Session 数据，所有的服务器都可以访问。

**复杂业务场景**：通过 Redis 以及 Redis 扩展（比如 Redisson）提供的数据结构，我们可以很方便地完成很多复杂的业务场景比如通过 Bitmap 统计活跃用户、通过 Sorted Set 维护排行榜。

## [如何基于 Redis 实现分布式锁？](https://javaguide.cn/database/redis/redis-questions-01.html#如何基于-redis-实现分布式锁)

[分布式锁详解](https://javaguide.cn/distributed-system/distributed-lock-implementations.html)

### [如何基于 Redis 实现一个最简易的分布式锁？](https://javaguide.cn/distributed-system/distributed-lock-implementations.html#如何基于-redis-实现一个最简易的分布式锁)

`SETNX` 即 **SET** if **N**ot e**X**ists (对应 Java 中的 `setIfAbsent` 方法)，如果 key 不存在的话，才会设置 key 的值。如果 key 已经存在， `SETNX` 啥也不做。

> key是锁名，value是标识符，区分锁

释放锁的话，直接通过 `DEL` 命令删除对应的 key 即可。

为了防止误删到其他的锁，这里我们建议使用 Lua 脚本（保证原子性）通过 key 对应的 value（唯一值）来判断。



![image-20240505220536821](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240505220536821.png)

优点：简单、高效

缺点：释放锁逻辑挂掉，可能导致锁无法释放

### [为什么要给锁设置一个过期时间？](https://javaguide.cn/distributed-system/distributed-lock-implementations.html#为什么要给锁设置一个过期时间)

为了避免锁无法被释放，我们可以想到的一个解决办法就是：**给这个 key（也就是锁） 设置一个过期时间** 。

**一定要保证设置指定 key 的值和过期时间是一个原子操作！！！** 不然的话，依然可能会出现锁无法被释放的问题。（设置key之后挂了，还是没设置过期时间）

缺点：

- 操作共享资源时间大于过期时间，锁提前过期，影响操作
- 锁超时时间过长，影响性能

### [如何实现锁的优雅续期？](https://javaguide.cn/distributed-system/distributed-lock-implementations.html#如何实现锁的优雅续期)

Redisson 中的分布式锁自带自动续期机制，使用起来非常简单，原理也比较简单，其提供了一个专门用来监控和续期锁的 **Watch Dog（ 看门狗）**

![image-20240505221556751](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240505221556751.png)

看门狗名字的由来于 `getLockWatchdogTimeout()` 方法，这个方法返回的是看门狗给锁续期的过期时间，默认为 30 秒。

`renewExpiration()` 方法包含了看门狗的主要逻辑，默认情况下，每过 10 秒，看门狗就会执行续期操作，将锁的超时时间设置为 30 秒。看门狗续期前也会先判断是否需要执行续期操作，需要才会执行续期，否则取消续期操作。

Watch Dog 通过调用 `renewExpirationAsync()` 方法实现锁的异步续期：`renewExpirationAsync` 方法其实是调用 Lua 脚本实现的续期，这样做主要是为了保证续期操作的原子性。

只有未指定锁超时时间，才会使用到 Watch Dog 自动续期机制。

```java
// 手动给锁设置过期时间，不具备 Watch Dog 自动续期机制
lock.lock(10, TimeUnit.SECONDS);
```

### [如何实现可重入锁？](#如何实现可重入锁)

所谓可重入锁指的是在一个线程中可以多次获取同一把锁，比如一个线程在执行一个带锁的方法，该方法中又调用了另一个需要相同锁的方法，则该线程可以直接执行调用的方法即可重入 ，而无需重新获得锁。像 Java 中的 `synchronized` 和 `ReentrantLock` 都属于可重入锁。

**不可重入的分布式锁基本可以满足绝大部分业务场景了，一些特殊的场景可能会需要使用可重入的分布式锁**

核心思路：判断是否为自己的锁，可以为每个锁关联一个可重入计数器和一个占有它的线程。当可重入计数器大于 0 时，则锁被占有，需要判断占有该锁的线程和请求获取锁的线程是否为同一个

实际项目中使用：**Redisson** ，其内置了多种类型的锁比如可重入锁（Reentrant Lock）、自旋锁（Spin Lock）、公平锁（Fair Lock）、多重锁（MultiLock）、 红锁（RedLock）、 读写锁（ReadWriteLock）。

### [Redis 如何解决集群情况下分布式锁的可靠性？](#redis-如何解决集群情况下分布式锁的可靠性)

为了避免单点故障，生产环境下的 Redis 服务通常是集群化部署的。

Redis 集群下，可能存在的问题。

![image-20240505222715474](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240505222715474.png)

Redis 之父 antirez 设计了 [Redlock 算法](https://redis.io/topics/distlock) 来解决。

让客户端向 Redis 集群中的多个独立的 Redis 实例依次请求申请加锁，如果客户端能够和半数以上的实例成功地完成加锁操作，那么我们就认为，客户端成功地获得分布式锁，否则加锁失败。

有半数节点可用，分布式锁就正常。

Redlock 是直接操作 Redis 节点的，并不是通过 Redis 集群操作的，这样才可以避免 Redis 集群主从切换导致的锁丢失问题。

Redlock 实现比较复杂，性能比较差，发生时钟变迁的情况下还存在安全性隐患。实际项目中不建议使用 Redlock 算法，成本和收益不成正比。

如果不是非要实现绝对可靠的分布式锁的话，其实单机版 Redis 就完全够了，实现简单，性能也非常高。

如果你必须要实现一个绝对可靠的分布式锁的话，可以基于 ZooKeeper 来做，只是性能会差一些。

或者使用Redisson的MutiLock锁，使用这把锁时节点不区分主从，每个节点的地位都是一样的， 这把锁加锁的逻辑需要写入到每一个主丛节点上，只有所有的服务器都写入成功，此时才是加锁成功，假设现在某个节点挂了，那么他去获得锁的时候，只要有一个节点拿不到，都不能算是加锁成功，就保证了加锁的可靠性。

> 修复这个节点之后才能拿到锁？

明显也会影响性能。

## [如何基于 Redis 实现延时任务？](#如何基于-redis-实现延时任务)

> 类似的问题：
>
> - 订单在 10 分钟后未支付就失效，如何用 Redis 实现？
> - 红包 24 小时未被查收自动退还，如何用 Redis 实现？

基于 Redis 实现延时任务的功能无非就下面两种方案：

1. Redis 过期事件监听
2. Redisson 内置的延时队列

Redis 过期事件监听的存在时效性较差、丢消息、多服务实例下消息重复消费等问题，不被推荐使用。

Redisson 的延迟队列 DelayedQueue 是基于 Redis 的 SortedSet 来实现的。SortedSet 是一个有序集合，其中的每个元素都可以设置一个分数，代表该元素的权重。Redisson 利用这一特性，将需要延迟执行的任务插入到 SortedSet 中，并给它们设置相应的过期时间作为分数。

Redisson 使用 `zrangebyscore` 命令扫描 SortedSet 中过期的元素，然后将这些过期元素从 SortedSet 中移除，并将它们加入到就绪消息列表中。就绪消息列表是一个阻塞队列，有消息进入就会被监听到。这样做可以避免对整个 SortedSet 进行轮询，提高了执行效率。

Redisson 内置的延时队列具备下面这些优势：

1. **减少了丢消息的可能**：DelayedQueue 中的消息会被持久化，即使 Redis 宕机了，根据持久化机制，也只可能丢失一点消息，影响不大。可以使用扫描数据库的方法作为补偿机制。
2. **消息不存在重复消费问题**：每个客户端都是从同一个目标队列中获取任务的，不存在重复消费的问题

> 上边的做法都没有消息队列靠谱

## [Redis 可以做消息队列么？](#redis-可以做消息队列么)

> 实际项目中使用 Redis 来做消息队列的非常少，毕竟有更成熟的消息队列中间件可以用。

先说结论：**可以是可以，但不建议使用 Redis 来做消息队列。和专业的消息队列相比，还是有很多欠缺的地方**。

:one:redis2.0之前，通过List，通过 `RPUSH/LPOP` 或者 `LPUSH/RPOP`即可实现简易版消息队列

通过轮询调用`RPOP`或者`LPOP`，如果为空，请求无效，浪费资源

Redis 还提供了 `BLPOP`、`BRPOP` 这种阻塞式读取的命令（带 B-Blocking 的都是阻塞式），并且还支持一个超时参数，如果 List 为空，Redis 服务端不会立刻返回结果，它会等待 List 中有新数据后再返回或者是等待最多一个超时时间后返回空。如果将超时时间设置为 0 时，即可无限等待，直到弹出消息

缺点：确认机制要自己实现、没有广播机制、消息只能被消费一次

:two:**Redis 2.0 引入了发布订阅 (pub/sub) 功能，解决了 List 实现消息队列没有广播机制的问题。**

![image-20240428202120939](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428202120939.png)

pub/sub 中引入了一个概念叫 **channel（频道）**，发布订阅机制的实现就是基于这个 channel 来做的。

pub/sub 涉及发布者（Publisher）和订阅者（Subscriber，也叫消费者）两个角色：

- 发布者通过 `PUBLISH` 投递消息给指定 channel。
- 订阅者通过`SUBSCRIBE`订阅它关心的 channel。并且，订阅者可以订阅一个或者多个 channel。

优点：

- 支持单播/广播
- 支持channel的简单正则匹配

缺点，未解决：

- 消息丢失（客户端断开连接或者 Redis 宕机都会导致消息丢失）
- 消息堆积（发布者发布消息的时候不会管消费者的具体消费能力如何）

:three:为此，Redis 5.0 新增加的一个数据结构 `Stream` 来做消息队列。`Stream` 支持：

- 发布 / 订阅模式
- 按照消费者组进行消费（借鉴了 Kafka 消费者组的概念）
- 消息持久化（ RDB 和 AOF）
- ACK 机制（通过确认机制来告知已经成功处理了消息）
- 阻塞式获取消息

缺点：

- 发生故障后不能保证消息至少被消费一次

:star:不建议使用redis做消息队列，有更专业的`RocketMQ,Kafka`等

## [Redis 可以做搜索引擎么？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-可以做搜索引擎么)

Redis 是可以实现全文搜索引擎功能的，需要借助 **RediSearch** ，这是一个基于 Redis 的搜索引擎模块。

相比较于 Elasticsearch 来说，RediSearch 主要在下面两点上表现更优异一些：

1. 性能更优秀：依赖 Redis 自身的高性能，基于内存操作（Elasticsearch 基于磁盘）。
2. 较低内存占用实现快速索引：RediSearch 内部使用压缩的倒排索引，所以可以用较低的内存占用来实现索引的快速构建。

存在限制：

1. 数据量限制：
   1. Elasticsearch 可以支持 PB 级别的数据量，可以轻松扩展到多个节点，利用分片机制提高可用性和性能。
   2. RedisSearch 是基于 Redis 实现的，其能存储的数据量受限于 Redis 的内存容量，不太适合存储大规模的数据（内存昂贵，扩展能力较差）。
2. 分布式能力较差：
   1. Elasticsearch 是为分布式环境设计的，可以轻松扩展到多个节点。
   2. 虽然 RedisSearch 支持分布式部署，但在实际应用中可能会面临一些挑战，如数据分片、节点间通信、数据一致性等问题。
3. 聚合功能较弱：
   1. Elasticsearch 提供了丰富的聚合功能，
   2. 而 RediSearch 的聚合功能相对较弱，只支持简单的聚合操作。
4. 生态较差：
   1. Elasticsearch 可以轻松和常见的一些系统/软件集成比如 Hadoop、Spark、Kibana
   2.  RedisSearch 则不具备该优势。

> Elasticsearch 适用于全文搜索、复杂查询、实时数据分析和聚合的场景
>
> 而 RediSearch 适用于快速数据存储、缓存和简单查询的场景

# [Redis 数据类型](https://javaguide.cn/database/redis/redis-questions-01.html#redis-数据类型)

**5 种基础数据类型**：String（字符串）、List（列表）、Set（集合）、Hash（散列）、Zset（有序集合）。

**3 种特殊数据类型**：HyperLogLog（基数统计）、Bitmap （位图）、Geospatial (地理位置)。

详情见学习笔记中的原理篇。

## [String 的应用场景有哪些？](#string-的应用场景有哪些)

String 是 Redis 中最简单同时也是最常用的一个数据类型。它是一种二进制安全的数据类型，可以用来存储任何类型的数据比如字符串、整数、浮点数、图片（图片的 base64 编码或者解码或者图片的路径）、序列化后的对象。

String 的常见应用场景如下：

- 常规数据（比如 Session、Token、序列化后的对象、图片的路径）的缓存；
- 计数比如用户单位时间的请求数（简单限流可以用到）、页面单位时间的访问数；
- 分布式锁(利用 `SETNX key value` 命令可以实现一个最简易的分布式锁)；
- ……

## [String 还是 Hash 存储对象数据更好呢？](#string-还是-hash-存储对象数据更好呢)

- 存储特点：
  - String 存储的是序列化后的对象数据，存放的是整个对象。
  - Hash 是对对象的每个字段单独存储，可以获取部分字段的信息，也可以修改或者添加部分字段，节省网络流量。
- 适用场景：
  - 如果对象中某些字段需要经常变动或者经常需要单独查询对象中的个别字段信息，Hash 就非常适合。
  - String 存储相对来说更加节省内存，缓存相同数量的对象数据，String 消耗的内存约是 Hash 的一半。并且，存储具有多层嵌套的对象时也方便很多。如果系统对性能和资源消耗非常敏感的话，String 就非常适合。

## [String 的底层实现是什么？](https://javaguide.cn/database/redis/redis-questions-01.html#string-的底层实现是什么)

 [SDS](https://github.com/antirez/sds)（Simple Dynamic String，简单动态字符串）

![1653984822363](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/1653984624671.png)

有图右侧的五种实现，但只有后4种实际用到。Redis 会根据初始化的长度决定使用哪种类型，从而减少内存的使用。

SDS 相比于 C 语言中的字符串有如下提升：

1. **可以避免缓冲区溢出**：C 语言中的字符串被修改（比如拼接）时，一旦没有分配足够长度的内存空间，就会造成缓冲区溢出。SDS 被修改时，会先根据 len 属性检查空间大小是否满足要求，如果不满足，则先扩展至所需大小再进行修改操作。
2. **获取字符串长度的复杂度较低**：C 语言中的字符串的长度通常是经过遍历计数来实现的，时间复杂度为 O(n)。SDS 的长度获取直接读取 len 属性即可，时间复杂度为 O(1)。
3. **减少内存分配次数**：为了避免修改（增加/减少）字符串时，每次都需要重新分配内存（C 语言的字符串是这样的），SDS 实现了空间预分配和惰性空间释放两种优化策略。
   - 空间预分配：当 SDS 需要增加字符串时，Redis 会为 SDS 分配好内存，并且根据特定的算法分配多余的内存，这样可以减少连续执行字符串增长操作所需的内存重分配次数。
   - 惰性释放：当 SDS 需要减少字符串时，这部分内存不会立即被回收，会被记录下来，等待后续使用（支持手动释放，有对应的 API）。

1. **二进制安全**：C 语言中的字符串以空字符 `\0` 作为字符串结束的标识，这存在一些问题，像一些二进制文件（比如图片、视频、音频）就可能包括空字符，C 字符串无法正确保存。SDS 使用 len 属性判断字符串是否结束，不存在这个问题。

## [购物车信息用 String 还是 Hash 存储更好呢?](https://javaguide.cn/database/redis/redis-questions-01.html#购物车信息用-string-还是-hash-存储更好呢)

商品信息频繁变动，建议使用Hash

<img src="https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428231024179.png" alt="image-20240428231024179" style="zoom:50%;" />

那用户购物车信息的维护具体应该怎么操作呢？

- 用户添加商品就是往 Hash 里面增加新的 field 与 value；
- 查询购物车信息就是遍历对应的 Hash；
- 更改商品数量直接修改对应的 value 值（直接 set 或者做运算皆可）；
- 删除商品就是删除 Hash 中对应的 field；
- 清空购物车直接删除对应的 key 即可

> 实际场景下只保存一个商品id不能应对需求

## [使用 Redis 实现一个排行榜怎么做？](#使用-redis-实现一个排行榜怎么做)

Redis 中有一个叫做 `Sorted Set` （有序集合）的数据类型经常被用在各种排行榜的场景

相关的一些 Redis 命令: `ZRANGE` (从小到大排序)、 `ZREVRANGE` （从大到小排序）、`ZREVRANK` (指定元素排名)

:question:待补充指北中的部分

## [Redis 的有序集合底层为什么要用跳表，而不用平衡树、红黑树或者 B+树？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-的有序集合底层为什么要用跳表-而不用平衡树、红黑树或者-b-树)

:star:这道面试题很多大厂比较喜欢问，难度还是有点大的。

- 平衡树 vs 跳表：
  - 平衡树的插入、删除和查询的时间复杂度和跳表一样都是 **O(log n)**。对于范围查询来说，平衡树也可以通过中序遍历的方式达到和跳表一样的效果。
  - 但是它的每一次插入或者删除操作都需要保证整颗树左右节点的绝对平衡，只要不平衡就要通过旋转操作来保持平衡，这个过程是比较耗时的。
  - 跳表诞生的初衷就是为了克服平衡树的一些缺点。跳表使用概率平衡而不是严格强制的平衡，因此，跳表中的插入和删除算法比平衡树的等效算法简单得多，速度也快得多。

- 红黑树 vs 跳表：
  - 相比较于红黑树来说，跳表的实现也更简单一些，不需要通过旋转和染色（红黑变换）来保证黑平衡。
  - 并且，按照区间来查找数据这个操作，红黑树的效率没有跳表高。

- B+树 vs 跳表：
  - B+树更适合作为数据库和文件系统中常用的索引结构之一，它的核心思想是通过可能少的 IO 定位到尽可能多的索引来获得查询数据。
  - 对于 Redis 这种内存数据库来说，它对这些并不感冒，因为 Redis 作为内存数据库它不可能存储大量的数据，所以对于索引不需要通过 B+树这种方式进行维护，只需按照概率进行随机维护即可，节约内存。
  - 而且使用跳表实现 zset 时相较前者来说更简单一些，在进行插入时只需通过索引将数据插入到链表中合适的位置再随机维护一定高度的索引即可，也不需要像 B+树那样插入时发现失衡时还需要对节点分裂与合并。

:question:什么是概率平衡

## [Set 的应用场景是什么？](#set-的应用场景是什么)

Redis 中 `Set` 是一种无序集合，集合中的元素没有先后顺序但都唯一，有点类似于 Java 中的 `HashSet` 。

`Set` 的常见应用场景如下：

- 存放的数据不能重复的场景：网站 UV 统计（数据量巨大的场景还是 `HyperLogLog`更适合一些）、文章点赞、动态点赞等等。
- 需要获取多个数据源交集、并集和差集的场景：共同好友(交集)、共同粉丝(交集)、共同关注(交集)、好友推荐（差集）、音乐推荐（差集）、订阅号推荐（差集+交集） 等等。
- 需要随机获取数据源中的元素的场景：抽奖系统、随机点名等等

## [使用 Set 实现抽奖系统怎么做？](#使用-set-实现抽奖系统怎么做)

如果想要使用 `Set` 实现一个简单的抽奖系统的话，直接使用下面这几个命令就可以了：

- `SADD key member1 member2 ...`：向指定集合添加一个或多个元素。
- `SPOP key count`：随机移除并获取指定集合中一个或多个元素，适合不允许重复中奖的场景。
- `SRANDMEMBER key count` : 随机获取指定集合中指定数量的元素，适合允许重复中奖的场景。

## [使用 Bitmap 统计活跃用户怎么做？](#使用-bitmap-统计活跃用户怎么做)

Bitmap 存储的是连续的二进制数字（0 和 1），通过 Bitmap, 只需要一个 bit 位来表示某个元素对应的值或者状态，key 就是对应元素本身 。我们知道 8 个 bit 可以组成一个 byte，所以 Bitmap 本身会极大的节省储存空间。

你可以将 Bitmap 看作是一个存储二进制数字（0 和 1）的数组，数组中每个元素的下标叫做 offset（偏移量）。

<img src="https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240429000031886.png" alt="image-20240429000031886" style="zoom:50%;" />

## [使用 HyperLogLog 统计页面 UV 怎么做？](#使用-hyperloglog-统计页面-uv-怎么做)

UV：全称Unique Visitor，也叫独立访客量，是指通过互联网访问、浏览这个网页的自然人。1天内同一个用户多次访问该网站，只记录1次。

使用 HyperLogLog 统计页面 UV 主要需要用到下面这两个命令：

- `PFADD key element1 element2 ...`：添加一个或多个元素到 HyperLogLog 中。
- `PFCOUNT key1 key2`：获取一个或者多个 HyperLogLog 的唯一计数。

# [Redis 持久化机制（重要）](#redis-持久化机制-重要)

Redis 不同于 Memcached 的很重要一点就是，Redis 支持持久化，而且支持 3 种持久化方式:

- 快照（snapshotting，RDB，Redis Database Backup file（Redis数据备份文件））
- 只追加文件（append-only file, AOF）
- RDB 和 AOF 的混合持久化(Redis 4.0 新增)

## [RDB 持久化](https://javaguide.cn/database/redis/redis-persistence.html#rdb-持久化)

### [什么是 RDB 持久化？](https://javaguide.cn/database/redis/redis-persistence.html#什么是-rdb-持久化)

通过创建快照获得存储在内存里面的数据在 **某个时间点** 上的副本。简单来说就是把内存中的所有数据都记录到磁盘中。

创建的快照可以完成：

- 备份
- 复制到其他服务器创建相同数据的服务器副本（主从结构）
- 保留原地，重启时使用

RDB持久化在四种情况下会执行：

- 执行save命令
- 执行bgsave命令
- Redis停机时
- 触发RDB条件时

### [RDB 创建快照时会阻塞主线程吗？](#rdb-创建快照时会阻塞主线程吗)

Redis 提供了两个命令来生成 RDB 快照文件：

- `save` : 同步保存操作，会阻塞 Redis 主线程；
- `bgsave` : fork 出一个子进程，子进程执行，不会阻塞 Redis 主线程，默认选项。

> 这里说 Redis 主线程而不是主进程的主要是因为 Redis 启动之后主要是通过单线程的方式完成主要的工作。如果你想将其描述为 Redis 主进程，也没毛病。

## [AOF 持久化](https://javaguide.cn/database/redis/redis-persistence.html#aof-持久化)

### [什么是 AOF 持久化？](https://javaguide.cn/database/redis/redis-persistence.html#什么是-aof-持久化)

与快照持久化相比，AOF 持久化的实时性更好。默认情况下 Redis 没有开启 AOF（append only file）方式的持久化（Redis 6.0 之后已经默认是开启了），可以通过 `appendonly` 参数开启。

开启 AOF 持久化后每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到 AOF 缓冲区 `server.aof_buf` 中，然后再写入到 AOF 文件中（此时还在系统内核缓存区未同步到磁盘），最后再根据持久化方式（ `fsync`策略）的配置来决定何时将系统内核缓存区的数据同步到硬盘中的。

与RDB位置相同，通过`dir`参数设置，默认文件名`appendonly.aof`

### [AOF 工作基本流程是怎样的？](#aof-工作基本流程是怎样的)

AOF 持久化功能的实现可以简单分为 5 步：

1. **命令追加（append）**：所有的写命令会追加到 AOF 缓冲区中。
2. **文件写入（write）**：将 AOF 缓冲区的数据写入到 AOF 文件中。这一步需要调用`write`函数（系统调用），`write`将数据写入到了系统内核缓冲区之后直接返回了（延迟写）。注意！！！此时并没有同步到磁盘。
3. **文件同步（fsync）**：AOF 缓冲区根据对应的持久化方式（ `fsync` 策略）向硬盘做同步操作。这一步需要调用 `fsync` 函数（系统调用）， `fsync` 针对单个文件操作，对其进行强制硬盘同步，`fsync` 将阻塞直到写入磁盘完成后返回，保证了数据持久化。
4. **文件重写（rewrite）**：随着 AOF 文件越来越大，需要定期对 AOF 文件进行重写，达到压缩的目的。
5. **重启加载（load）**：当 Redis 重启时，可以加载 AOF 文件进行数据恢复。

`write`：写入系统内核缓冲区之后直接返回（仅仅是写到缓冲区），不会立即同步到硬盘。

- 虽然提高了效率，但也带来了数据丢失的风险。
- 同步硬盘操作通常依赖于系统调度机制，Linux 内核通常为 30s 同步一次，具体值取决于写出的数据量和 I/O 缓冲区的状态。

`fsync`：`fsync`用于强制刷新系统内核缓冲区（同步到到磁盘），确保写磁盘操作结束才会返回。

![image-20240429142801878](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240429142801878.png)

### [AOF 持久化方式有哪些？](#aof-持久化方式有哪些)

在 Redis 的配置文件中存在三种不同的 AOF 持久化方式（ `fsync`策略），它们分别是：

1. `appendfsync always`：主线程调用 `write` 执行写操作后，后台线程（ `aof_fsync` 线程）立即会调用 `fsync` 函数同步 AOF 文件（刷盘），`fsync` 完成后线程返回，这样会严重降低 Redis 的性能（`write` + `fsync`）。
2. `appendfsync everysec`：主线程调用 `write` 执行写操作后立即返回，由后台线程（ `aof_fsync` 线程）每秒钟调用 `fsync` 函数（系统调用）同步一次 AOF 文件（`write`+`fsync`，`fsync`间隔为 1 秒）
3. `appendfsync no`：主线程调用 `write` 执行写操作后立即返回，让操作系统决定何时进行同步，Linux 下一般为 30 秒一次（`write`但不`fsync`，`fsync` 的时机由操作系统决定）。

从 Redis 7.0.0 开始，Redis 使用了 **Multi Part AOF** 机制。顾名思义，Multi Part AOF 就是将原来的单个 AOF 文件拆分成多个 AOF 文件

### [AOF 为什么是在执行完命令之后记录日志？](https://javaguide.cn/database/redis/redis-persistence.html#aof-为什么是在执行完命令之后记录日志)

关系型数据库（如 MySQL）通常都是执行命令之前记录日志（方便故障恢复），而 Redis AOF 持久化机制是在执行完命令之后再记录日志

**为什么是在执行完命令之后记录日志呢？**

- 避免额外的检查开销，AOF 记录日志不会对命令进行语法检查；
- 在命令执行完之后再记录，不会阻塞当前的命令执行。

这样也带来了风险：

- 如果刚执行完命令 Redis 就宕机会导致对应的修改丢失；
- 可能会阻塞后续其他命令的执行（AOF 记录日志是在 Redis 主线程中进行的）

### [AOF 重写了解吗？](https://javaguide.cn/database/redis/redis-persistence.html#aof-重写了解吗)

当 AOF 变得太大时，Redis 能够在后台自动重写 AOF 产生一个新的 AOF 文件，这个新的 AOF 文件和原有的 AOF 文件所保存的数据库状态一样，但体积更小。

> AOF 重写（rewrite） 是一个有歧义的名字，该功能是通过读取数据库中的键值对来实现的，程序无须对现有 AOF 文件进行任何读入、分析或者写入操作。

AOF重写会造成大量写入操作，避免对正常请求造成影响，将AOF重写放入子进程中

重写期间会维护**AOF缓冲区**，在子进程创建新的AOF文件期间，记录执行的命令，重写完成后将这些命令追加到新AOF文件的末尾

开启 AOF 重写功能，可以调用 `BGREWRITEAOF` 命令手动执行，也可以设置下面两个配置项，让程序自动决定触发时机：

- `auto-aof-rewrite-min-size`：如果 AOF 文件大小小于该值，则不会触发 AOF 重写。默认值为 64 MB;
- `auto-aof-rewrite-percentage`：执行 AOF 重写时，当前 AOF 大小（aof_current_size）和上一次重写时 AOF 大小（aof_base_size）的比值。如果当前 AOF 文件大小增加了这个百分比值，将触发 AOF 重写。将此值设置为 0 将禁用自动 AOF 重写。默认值为 100。

### [AOF 校验机制了解吗？](#aof-校验机制了解吗)

AOF 校验机制是 Redis 在启动时对 AOF 文件进行检查，以判断文件是否完整，是否有损坏或者丢失的数据。这个机制的原理其实非常简单，就是通过使用一种叫做 **校验和（checksum）** 的数字来验证 AOF 文件。

校验和由对整个AOF文件进行CRC64算法得到。启动时候会计算保存到文件末尾的校验和与此时计算出的是否相等。

> RDB也有类似的

## [Redis 4.0 对于持久化机制做了什么优化？](#redis-4-0-对于持久化机制做了什么优化)

由于 RDB 和 AOF 各有优势，于是，Redis 4.0 开始支持 RDB 和 AOF 的混合持久化（默认关闭，可以通过配置项 `aof-use-rdb-preamble` 开启）。

如果把混合持久化打开，AOF 重写的时候就直接把 RDB 的内容写到 AOF 文件开头。

优点：快速加载（RDB）同时避免丢失过多数据(AOF)

缺点：AOF里边RDB部分是压缩格式，不再是AOF格式，可读性较差

## [如何选择 RDB 和 AOF？](https://javaguide.cn/database/redis/redis-persistence.html#如何选择-rdb-和-aof)

**RDB 比 AOF 优秀的地方**：

- RDB 文件存储的内容是经过压缩的二进制数据， 保存着某个时间点的数据集，文件很小，适合做数据的备份，灾难恢复。AOF 文件存储的是每一次写命令，类似于 MySQL 的 binlog 日志，通常会比 RDB 文件大很多。当 AOF 变得太大时，Redis 能够在后台自动重写 AOF。新的 AOF 文件和原有的 AOF 文件所保存的数据库状态一样，但体积更小。不过， Redis 7.0 版本之前，如果在重写期间有写入命令，AOF 可能会使用大量内存，重写期间到达的所有写入命令都会写入磁盘两次。
- 使用 RDB 文件恢复数据，直接解析还原数据即可，不需要一条一条地执行命令，速度非常快。而 AOF 则需要依次执行每个写命令，速度非常慢。也就是说，与 AOF 相比，恢复大数据集的时候，RDB 速度更快。

**AOF 比 RDB 优秀的地方**：

- RDB 的数据安全性不如 AOF，没有办法实时或者秒级持久化数据。生成 RDB 文件的过程是比较繁重的， 虽然 BGSAVE 子进程写入 RDB 文件的工作不会阻塞主线程，但会对机器的 CPU 资源和内存资源产生影响，严重的情况下甚至会直接把 Redis 服务干宕机。AOF 支持秒级数据丢失（取决 fsync 策略，如果是 everysec，最多丢失 1 秒的数据），仅仅是追加命令到 AOF 文件，操作轻量。
- RDB 文件是以特定的二进制格式保存的，并且在 Redis 版本演进中有多个版本的 RDB，所以存在老版本的 Redis 服务不兼容新版本的 RDB 格式的问题。
- AOF 以一种易于理解和解析的格式包含所有操作的日志。你可以轻松地导出 AOF 文件进行分析，你也可以直接操作 AOF 文件来解决一些问题。比如，如果执行`FLUSHALL`命令意外地刷新了所有内容后，只要 AOF 文件没有被重写，删除最新命令并重启即可恢复之前的状态。

**综上**：

- Redis 保存的数据丢失一些也没什么影响的话，可以选择使用 RDB。
- 不建议单独使用 AOF，因为时不时地创建一个 RDB 快照可以进行数据库备份、更快的重启以及解决 AOF 引擎错误。
- 如果保存的数据要求安全性比较高的话，建议同时开启 RDB 和 AOF 持久化或者开启 RDB 和 AOF 混合持久化。

![image-20210725152037611](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20210725151940515.png)

# [Redis 线程模型（重要）](https://javaguide.cn/database/redis/redis-questions-01.html#redis-线程模型-重要)

对于读写命令来说，Redis 一直是单线程模型。

在 Redis 4.0 版本之后引入了多线程来执行一些大键值对的异步删除操作

Redis 6.0 版本之后引入了多线程来处理网络请求（提高网络 IO 读写性能）。

## [Redis 单线程模型了解吗？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-单线程模型了解吗)

**Redis 基于 Reactor 模式设计开发了一套高效的事件处理模型** （Netty 的线程模型也基于 Reactor 模式，Reactor 模式不愧是高性能 IO 的基石），这套事件处理模型对应的是 Redis 中的文件事件处理器（file event handler）。由于文件事件处理器（file event handler）是单线程方式运行的，所以我们一般都说 Redis 是单线程模型。

文件事件处理器（file event handler）主要是包含 4 个部分：

- 多个 socket（客户端连接）
- IO 多路复用程序（支持多个客户端连接的关键）
- 文件事件分派器（将 socket 关联到相应的事件处理器）
- 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器）

文件事件处理器使用 I/O 多路复用（multiplexing）程序来同时监听多个套接字，并根据套接字目前执行的任务来为套接字关联不同的事件处理器。

当被监听的套接字准备好执行连接应答（accept）、读取（read）、写入（write）、关 闭（close）等操作时，与操作相对应的文件事件就会产生，这时文件事件处理器就会调用套接字之前关联好的事件处理器来处理这些事件。

**虽然文件事件处理器以单线程方式运行，但通过使用 I/O 多路复用程序来监听多个套接字**，文件事件处理器既实现了高性能的网络通信模型，又可以很好地与 Redis 服务器中其他同样以单线程方式运行的模块进行对接，这保持了 Redis 内部单线程设计的简单性

![image-20240429145851884](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240429145851884.png)

基于`epoll`函数的IO多路复用模型

![1653902845082](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/1653902845082.png)

## [Redis6.0 之前为什么不使用多线程？](https://javaguide.cn/database/redis/redis-questions-01.html#redis6-0-之前为什么不使用多线程)

虽然说 Redis 是单线程模型，但是，实际上，**Redis 在 4.0 之后的版本中就已经加入了对多线程的支持。**

不过，Redis 4.0 增加的多线程主要是针对一些大键值对的删除操作的命令，使用这些命令就会使用主线程之外的其他线程来“异步处理”。

为此，Redis 4.0 之后新增了`UNLINK`（可以看作是 `DEL` 的异步版本）、`FLUSHALL ASYNC`（清空所有数据库的所有 key，不仅仅是当前 `SELECT` 的数据库）、`FLUSHDB ASYNC`（清空当前 `SELECT` 数据库中的所有 key）等异步命令。

**那 Redis6.0 之前为什么不使用多线程？** 我觉得主要原因有 3 点：

- 单线程编程容易并且更容易维护；
- Redis 的性能瓶颈不在 CPU ，主要在内存和网络；
- 多线程就会存在死锁、线程上下文切换等问题，甚至会影响性能。

## [Redis6.0 之后为何引入了多线程？](https://javaguide.cn/database/redis/redis-questions-01.html#redis6-0-之后为何引入了多线程)

**Redis6.0 引入多线程主要是为了提高网络 IO 读写性能**，因为这个算是 Redis 中的一个性能瓶颈（Redis 的瓶颈主要受限于内存和网络）。

 Redis 的多线程只是在网络数据的读写这类耗时操作上使用了，执行命令仍然是单线程顺序执行，没有并发安全问题。

Redis6.0 的多线程默认是禁用的，只使用主线程。如需开启需要设置 IO 线程数 > 1，需要修改 redis 配置文件 `redis.conf`

```conf
io-threads 4 #设置1的话只会开启主线程，官网建议4核的机器建议设置为2或3个线程，8核的建议设置为6个线程
```

- io-threads 的个数一旦设置，不能通过 config 动态设置。
- 当设置 ssl 后，io-threads 将不工作。

开启之后只是默认多线程写（发送给客户端），需要多线程读，修改`redis.conf`:

```
io-threads-do-reads yes
```

> 官方描述并未太大提升，不建议开启

## [Redis 后台线程了解吗？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-后台线程了解吗)

我们虽然经常说 Redis 是单线程模型（主要逻辑是单线程完成的），但实际还有一些后台线程用于执行一些比较耗时的操作：

- 通过 `bio_close_file` 后台线程来释放 AOF / RDB 等过程中产生的临时文件资源。
- 通过 `bio_aof_fsync` 后台线程调用 `fsync` 函数将系统内核缓冲区还未同步到到磁盘的数据强制刷到磁盘（ AOF 文件）。
- 通过 `bio_lazy_free`后台线程释放大对象（已删除）占用的内存空间.

# [Redis 内存管理](https://javaguide.cn/database/redis/redis-questions-01.html#redis-内存管理)

## [Redis 给缓存数据设置过期时间有啥用？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-给缓存数据设置过期时间有啥用)

- 内存有限，一直保存会OOM
- 适用于验证码，Token有效时间的场景

> **Redis 中除了字符串类型有自己独有设置过期时间的命令 `setex` 外，其他方法都需要依靠 `expire` 命令来设置过期时间 。另外， `persist` 命令可以移除一个键的过期时间。**

## [Redis 是如何判断数据是否过期的呢？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-是如何判断数据是否过期的呢)

Redis 通过一个叫做过期字典（可以看作是 hash 表）来保存数据过期的时间。

过期字典的键指向 Redis 数据库中的某个 key(键)，过期字典的值是一个 long long 类型的整数，这个整数保存了 key 所指向的数据库键的过期时间（毫秒精度的 UNIX 时间戳）。

Dict结构中，一个表存key-value，一个表存key-TTL

![1653983366243](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/1653983606531.png)

## [过期的数据的删除策略了解么？](https://javaguide.cn/database/redis/redis-questions-01.html#过期的数据的删除策略了解么)

1. **惰性删除**：只会在取出 key 的时候才对数据进行过期检查。这样对 CPU 最友好，但是可能会造成太多过期 key 没有被删除。
2. **定期删除**：每隔一段时间抽取一批 key 执行删除过期 key 操作。并且，Redis 底层会通过限制删除操作执行的时长和频率来减少删除操作对 CPU 时间的影响。

定期删除对内存更加友好，惰性删除对 CPU 更加友好。两者各有千秋，所以 Redis 采用的是 **定期删除+惰性/懒汉式删除** 。

## [Redis 内存淘汰机制了解么？](https://javaguide.cn/database/redis/redis-questions-01.html#redis-内存淘汰机制了解么)

Redis支持8种不同策略来选择要删除的key：

* noeviction： 不淘汰任何key，但是内存满时不允许写入新数据，默认就是这种策略。
* volatile-ttl： 对设置了TTL的key，比较key的剩余TTL值，TTL越小越先被淘汰
* allkeys-random：对全体key ，随机进行淘汰。也就是直接从db->dict中随机挑选
* volatile-random：对设置了TTL的key ，随机进行淘汰。也就是从db->expires中随机挑选。
* allkeys-lru： 对全体key，基于LRU算法进行淘汰
* volatile-lru： 对设置了TTL的key，基于LRU算法进行淘汰
* allkeys-lfu： 对全体key，基于LFU算法进行淘汰
* volatile-lfu： 对设置了TTL的key，基于LFI算法进行淘汰
  比较容易混淆的有两个：
  * LRU（Least Recently Used），最少最近使用。用当前时间减去最后一次访问时间，这个值越大则淘汰优先级越高。
  * LFU（Least Frequently Used），最少频率使用。会统计每个key的访问频率，值越小淘汰优先级越高。

> LFU的策略是4.0之后才加入的

# [Redis 事务](https://javaguide.cn/database/redis/redis-questions-02.html#redis-事务)

## [什么是 Redis 事务？](https://javaguide.cn/database/redis/redis-questions-02.html#什么是-redis-事务)

你可以将 Redis 中的事务理解为：**Redis 事务提供了一种将多个命令请求打包的功能。然后，再按顺序执行打包的所有命令，并且不会被中途打断。**

## [如何使用 Redis 事务？](https://javaguide.cn/database/redis/redis-questions-02.html#如何使用-redis-事务)

[`MULTI`](https://redis.io/commands/multi) 命令后可以输入多个命令，Redis 不会立即执行这些命令，而是将它们放到队列，当调用了 [`EXEC`](https://redis.io/commands/exec) 命令后，再执行所有的命令

通过 [`DISCARD`](https://redis.io/commands/discard) 命令取消一个事务，它会清空事务队列中保存的所有命令

通过[`WATCH`](https://redis.io/commands/watch) 命令监听指定的 Key，当调用 `EXEC` 命令执行事务时，如果一个被 `WATCH` 命令监视的 Key 被 **其他客户端/Session** 修改的话，整个事务都不会被执行

>  **WATCH** 与 **事务** 在同一个 Session 里，被监视的Key的修改操作发生在事务内部，是能执行成功的

## [Redis 事务支持原子性吗？](https://javaguide.cn/database/redis/redis-questions-02.html#redis-事务支持原子性吗)

不支持

Redis 事务在运行错误的情况下，除了执行过程中出现错误的命令外，其他命令都能正常执行。并且，Redis 事务是不支持回滚（roll back）操作的。

## [Redis 事务支持持久性吗？](https://javaguide.cn/database/redis/redis-questions-02.html#redis-事务支持持久性吗)

RDB和AOF没法保证数据不丢失

:question:但是mysql凭借redo log能保证数据完全不丢失么？

## [如何解决 Redis 事务的缺陷？](https://javaguide.cn/database/redis/redis-questions-02.html#如何解决-redis-事务的缺陷)

Redis 从 2.6 版本开始支持执行 Lua 脚本，它的功能和事务非常类似。我们可以利用 Lua 脚本来批量执行多条 Redis 命令，这些 Redis 命令会被提交到 Redis 服务器一次性执行完成，大幅减小了网络开销。

出错的时候，错误之后不被执行，错误之前不能回滚

Redis 7.0 新增了 [Redis functions](https://redis.io/docs/manual/programmability/functions-intro/) 特性，你可以将 Redis functions 看作是比 Lua 更强大的脚本

# [Redis 性能优化（重要）](https://javaguide.cn/database/redis/redis-questions-02.html#redis-性能优化-重要)

## [使用批量操作减少网络传输](https://javaguide.cn/database/redis/redis-questions-02.html#使用批量操作减少网络传输)

一个 Redis 命令的执行可以简化为以下 4 步：

1. 发送命令
2. 命令排队
3. 命令执行
4. 返回结果

其中，第 1 步和第 4 步耗费时间之和称为 **Round Trip Time (RTT,往返时间)** ，也就是数据在网络上传输的时间。

使用批量操作可以减少网络传输次数，进而有效减小网络开销，大幅减少 RTT。

另外，除了能减少 RTT 之外，发送一次命令的 socket I/O 成本也比较高（涉及上下文切换，存在`read()`和`write()`系统调用），批量操作还可以减少 socket I/O 成本

### [原生批量操作命令](https://javaguide.cn/database/redis/redis-questions-02.html#原生批量操作命令)

Redis 中有一些原生支持批量操作的命令，比如：

- `MGET`(获取一个或多个指定 key 的值)、`MSET`(设置一个或多个指定 key 的值)、
- `HMGET`(获取指定哈希表中一个或者多个指定字段的值)、`HMSET`(同时将一个或多个 field-value 对设置到指定哈希表中)、
- `SADD`（向指定集合添加一个或多个元素）

Redis Cluster分片集群下，会产生问题。比如说 `MGET` 无法保证所有的 key 都在同一个 **hash slot**（哈希槽）上

### [pipeline](https://javaguide.cn/database/redis/redis-questions-02.html#pipeline)

对于不支持批量操作的命令，我们可以利用 **pipeline（流水线)** 将一批 Redis 命令封装成一组，这些 Redis 命令会被一次性提交到 Redis 服务器，只需要一次网络传输。

注意控制元素个数，避免网络传输的数据量过大

同样存在hash slot问题

原生批量操作命令和 pipeline 的是有区别的，使用的时候需要注意：

- 原生批量操作命令是原子操作，pipeline 是非原子操作。
- pipeline 可以打包不同的命令，原生批量操作命令不可以。
- 原生批量操作命令是 Redis 服务端支持实现的，而 pipeline 需要服务端和客户端的共同实现。

pipeline 和 Redis 事务的对比：

- 事务是原子操作，pipeline 是非原子操作。两个不同的事务不会同时运行，而 pipeline 可以同时以交错方式执行。

  > 因为这个特性，pipeline不能用于执行有顺序依赖的批命令

- Redis 事务中每个命令都需要发送到服务端，而 Pipeline 只需要发送一次，请求次数更少。

### [Lua 脚本](https://javaguide.cn/database/redis/redis-questions-02.html#lua-脚本)

Lua 脚本同样支持批量操作多条命令。一段 Lua 脚本可以视作一条命令执行，可以看作是 **原子操作** 。

一段Lua脚本执行过程中不会有其他脚本或者Redis命令同时执行，pipeline没有

并且，Lua 脚本中支持一些简单的逻辑处理比如使用命令读取值并在 Lua 脚本中进行处理，这同样是 pipeline 所不具备的。

缺陷：

- 如果 Lua 脚本运行时出错并中途结束，之后的操作不会进行，但是之前已经发生的写操作不会撤销，所以即使使用了 Lua 脚本，也不能实现类似数据库回滚的原子性。
- Redis Cluster 下 Lua 脚本的原子操作也无法保证了，原因同样是无法保证所有的 key 都在同一个 **hash slot**（哈希槽）上

### [集群下的批处理]()

![image-20240328204029826](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240328204029826.png)

实际环境中，spring为我们提供了一定的封装方法。

在`RedisAdvancedClusterAsyncCommandsImpl`类中

首先根据slotHash算出来一个partitioned的map，map中的key就是slot，而他的value就是对应的对应相同slot的key对应的数据

通过 `RedisFuture<String> mset = super.mset(op);`进行异步的消息发送

## [大量 key 集中过期问题](https://javaguide.cn/database/redis/redis-questions-02.html#大量-key-集中过期问题)

我在前面提到过：对于过期 key，Redis 采用的是 **定期删除+惰性/懒汉式删除** 策略。

定期删除执行过程中，如果突然遇到大量过期 key 的话，客户端请求必须等待定期清理过期 key 任务线程执行完成，因为这个这个定期任务线程是在 Redis 主线程中执行的。这就导致客户端请求没办法被及时处理，响应速度会比较慢。

解决方案：

1. 给 key 设置随机过期时间。
2. 开启 lazy-free（惰性删除/延迟释放） 。lazy-free 特性是 Redis 4.0 开始引入的，指的是让 Redis 采用异步方式延迟释放 key 使用的内存，将该操作交给单独的子线程处理，避免阻塞主线程。

个人建议不管是否开启 lazy-free，我们都尽量给 key 设置随机过期时间。

## [Redis bigkey（大 Key）](https://javaguide.cn/database/redis/redis-questions-02.html#redis-bigkey-大-key)

### [什么是 bigkey？](https://javaguide.cn/database/redis/redis-questions-02.html#什么是-bigkey)

BigKey通常以Key的大小和Key中成员的数量来综合判定，例如：

- Key本身的数据量过大：一个String类型的Key，它的值为5 MB
- Key中的成员数过多：一个ZSET类型的Key，它的成员数量为10,000个
- Key中成员的数据量过大：一个Hash类型的Key，它的成员数量虽然只有1,000个但这些成员的Value（值）总大小为100 MB

### [bigkey的产生原因及其危害]()

- 程序设计不当，比如直接用String类型存储较大的文件对应的二进制
- 对业务规模考虑不周到，使用集合时没有考虑到数据量的快速增长
- 没及时清理垃圾数据

危害：

- 网络阻塞
  - 对BigKey执行读请求时，少量的QPS就可能导致带宽使用率被占满，导致Redis实例，乃至所在物理机变慢
- 数据倾斜
  - BigKey所在的Redis实例内存使用率远超其他实例，无法使数据分片的内存资源达到均衡
- Redis阻塞
  - 对元素较多的hash、list、zset等做运算会耗时较旧，使主线程被阻塞
- CPU压力
  - 对BigKey的数据序列化和反序列化会导致CPU的使用率飙升，影响Redis实例和本机其它应用

### [如何发现bigkey]()

①redis-cli --bigkeys

利用redis-cli提供的--bigkeys参数，可以遍历分析所有key，并返回Key的整体统计信息与每个数据的Top1的big key

命令：`redis-cli -a 密码 --bigkeys`

> 会扫描(Scan) Redis 中的所有 key ，会对 Redis 的性能有一点影响
>
> 只返回top1，不太严谨，top2可能也是

②scan扫描

自己编程，利用scan扫描Redis中的所有key，利用strlen、hlen等命令判断key的长度（此处不建议使用MEMORY USAGE(4.0之后对集合的方法)，会对cpu造成较大的消耗）

![image-20240430095057091](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240430095057091.png)

③第三方工具

- 利用第三方工具，如 Redis-Rdb-Tools 分析RDB快照文件，全面分析内存使用情况

④网络监控

- 自定义工具，监控进出Redis的网络数据，超出预警值时主动告警
- 一般阿里云搭建的云服务器就有相关监控页面

### [如何处理bigkey]()

- **分割 bigkey**：将一个 bigkey 分割为多个小 key。例如，将一个含有上万字段数量的 Hash 按照一定策略（比如二次哈希）拆分为多个 Hash。
- **手动清理**：Redis 4.0+ 可以使用 `UNLINK` 命令来异步删除一个或多个指定的 key。Redis 4.0 以下可以考虑使用 `SCAN` 命令结合 `DEL` 命令来分批次删除。
- **采用合适的数据结构**：例如，文件二进制数据不使用 String 保存、使用 HyperLogLog 统计页面 UV、Bitmap 保存状态信息（0/1）。
- **开启 lazy-free（惰性删除/延迟释放）** ：lazy-free 特性是 Redis 4.0 开始引入的，指的是让 Redis 采用异步方式延迟释放 key 使用的内存，将该操作交给单独的子线程处理，避免阻塞主线程
- 集合类型，先删除其中的子元素，最后再删除bigkey

## [Redis hotkey（热 Key）](https://javaguide.cn/database/redis/redis-questions-02.html#redis-hotkey-热-key)

### [什么是 hotkey？](https://javaguide.cn/database/redis/redis-questions-02.html#什么是-hotkey)

如果一个 key 的访问次数比较多且明显多于其他 key 的话，那这个 key 就可以看作是 **hotkey（热 Key）**。

hotkey 出现的原因主要是某个热点数据访问量暴增，如重大的热搜事件、参与秒杀的商品。

### [hotkey 有什么危害？](https://javaguide.cn/database/redis/redis-questions-02.html#hotkey-有什么危害)

处理 hotkey 会占用大量的 CPU 和带宽，可能会影响 Redis 实例对其他请求的正常处理。

此外，如果突然访问 hotkey 的请求超出了 Redis 的处理能力，Redis 就会直接宕机。这种情况下，大量请求将落到后面的数据库上，可能会导致数据库崩溃。

因此，hotkey 很可能成为系统性能的瓶颈点，需要单独对其进行优化，以确保系统的高可用性和稳定性。

### [如何发现 hotkey？](https://javaguide.cn/database/redis/redis-questions-02.html#如何发现-hotkey)

**1、使用 Redis 自带的 `--hotkeys` 参数来查找。**

Redis 4.0.3 版本中新增了 `hotkeys` 参数，该参数能够返回所有 key 的被访问次数。

使用该方案的前提条件是 Redis Server 的 `maxmemory-policy` 参数设置为 LFU 算法（内存淘汰策略）

> 会增加 Redis 实例的 CPU 和内存消耗（全局扫描）

**2、使用`MONITOR` 命令。**

`MONITOR` 命令是 Redis 提供的一种实时查看 Redis 的所有操作的方式，可以用于临时监控 Redis 实例的操作情况，包括读写、删除等操作。

> 对性能影响大，禁止长时间开启

**3、借助开源项目。**

**4、根据业务情况提前预估。**

可以根据业务情况来预估一些 hotkey，比如参与秒杀活动的商品数据等。不过，我们无法预估所有 hotkey 的出现，比如突发的热点新闻事件等。

**5、业务代码中记录分析。**

在业务代码中添加相应的逻辑对 key 的访问情况进行记录分析。不过，这种方式会让业务代码的复杂性增加，一般也不会采用

**6、借助公有云的 Redis 分析服务。**

### [如何解决 hotkey？](https://javaguide.cn/database/redis/redis-questions-02.html#如何解决-hotkey)

hotkey 的常见处理以及优化办法如下（这些方法可以配合起来使用）：

- **读写分离**：主节点处理写请求，从节点处理读请求。
- **使用 Redis Cluster**：将热点数据分散存储在多个 Redis 节点上。
- **二级缓存**：hotkey 采用二级缓存的方式进行处理，将 hotkey 存放一份到 JVM 本地内存中（可以用 Caffeine）。

> 公有云的 Redis 服务话，还可以留意其提供的开箱即用的解决方案

# [慢查询命令](https://javaguide.cn/database/redis/redis-questions-02.html#慢查询命令)

## [为什么会有慢查询命令？](https://javaguide.cn/database/redis/redis-questions-02.html#为什么会有慢查询命令)

redis执行的步骤：

1. 发送命令
2. 命令排队
3. 命令执行
4. 返回结果

Redis 中的大部分命令都是 O(1)时间复杂度，但也有少部分 O(n) 时间复杂度的命令：

- `KEYS *`：会返回所有符合规则的 key。
- `HGETALL`：会返回一个 Hash 中所有的键值对。
- `LRANGE`：会返回 List 中指定范围内的元素。
- `SMEMBERS`：返回 Set 中的所有元素。
- `SINTER`/`SUNION`/`SDIFF`：计算多个 Set 的交集/并集/差集。

这些命令并不是一定不能使用，但是需要明确 N 的值。另外，有遍历的需求可以使用 `HSCAN`、`SSCAN`、`ZSCAN` 代替（他们可以一次获得一部分，而不是一下子全部获取完）

复杂度可能高于O(n)的指令：

- `ZRANGE`/`ZREVRANGE`：返回指定 Sorted Set 中指定排名范围内的所有元素。时间复杂度为 O(log(n)+m)，n 为所有元素的数量， m 为返回的元素数量，当 m 和 n 相当大时，O(n) 的时间复杂度更小。
- `ZREMRANGEBYRANK`/`ZREMRANGEBYSCORE`：移除 Sorted Set 中指定排名范围/指定 score 范围内的所有元素。时间复杂度为 O(log(n)+m)，n 为所有元素的数量， m 被删除元素的数量，当 m 和 n 相当大时，O(n) 的时间复杂度更小。

## [如何找到慢查询命令？](https://javaguide.cn/database/redis/redis-questions-02.html#如何找到慢查询命令)

在 `redis.conf` 文件中，我们可以使用 `slowlog-log-slower-than` 参数设置耗时命令的阈值，并使用 `slowlog-max-len` 参数设置耗时命令的最大记录条数。

当 Redis 服务器检测到执行时间超过 `slowlog-log-slower-than`阈值的命令时，就会将该命令记录在慢查询日志(slow log) 中，这点和 MySQL 记录慢查询语句类似。当慢查询日志超过设定的最大记录条数之后，Redis 会把最早的执行命令依次舍弃

:warning:注意：由于慢查询日志会占用一定内存空间，如果设置最大记录条数过大，可能会导致内存占用过高的问题

可以使用`CONFIG`命令设置，通过`SLOWLOG GET`获取（默认最近10条，可设置），通过`SLOWLOG LEN`查看条数，通过`SLOWLOG RESET`清空

慢查询日志中的每个条目都由以下六个值组成：

1. 唯一渐进的日志标识符。
2. 处理记录命令的 Unix 时间戳。
3. 执行所需的时间量，以微秒为单位。
4. 组成命令参数的数组。
5. 客户端 IP 地址和端口。
6. 客户端名称。

# [Redis 内存碎片](https://javaguide.cn/database/redis/redis-questions-02.html#redis-内存碎片)

## [什么是内存碎片?](https://javaguide.cn/database/redis/redis-memory-fragmentation.html#什么是内存碎片)

你可以将内存碎片简单地理解为那些不可用的空闲内存。

## [为什么会有 Redis 内存碎片?](https://javaguide.cn/database/redis/redis-memory-fragmentation.html#为什么会有-redis-内存碎片)

Redis 内存碎片产生比较常见的 2 个原因：

**1、Redis 存储数据的时候向操作系统申请的内存空间可能会大于数据实际需要的存储空间。**

Redis 使用 `zmalloc` 方法(Redis 自己实现的内存分配方法)进行内存分配的时候，除了要分配 `size` 大小的内存之外，还会多分配 `PREFIX_SIZE` 大小的内存（数据类型等其他信息）。

另外，Redis 可以使用多种内存分配器来分配内存（ libc、jemalloc、tcmalloc），默认使用 [jemalloc](https://github.com/jemalloc/jemalloc)，而 jemalloc 按照一系列固定的大小（8 字节、16 字节、32 字节……）来分配内存的

当程序申请的内存最接近某个固定值时，jemalloc 会给它分配相应大小的空间，要17字节，最近的是32字节，分配32字节。但是专门针对内存碎片做了优化，不会存在过度碎片化的问题。

**2、频繁修改 Redis 中的数据也会产生内存碎片。**

当 Redis 中的某个数据删除时，Redis 通常不会轻易释放内存给操作系统。

## [如何查看 Redis 内存碎片的信息？](https://javaguide.cn/database/redis/redis-memory-fragmentation.html#如何查看-redis-内存碎片的信息)

使用 `info memory` 命令即可查看 Redis 内存相关的信息。

Redis 内存碎片率的计算公式：`mem_fragmentation_ratio` （内存碎片率）= `used_memory_rss` (操作系统实际分配给 Redis 的物理内存空间大小)/ `used_memory`(Redis 内存分配器为了存储数据实际申请使用的内存空间大小)

 `mem_fragmentation_ratio > 1.5` 的话才需要清理内存碎片。

## [如何清理 Redis 内存碎片？](https://javaguide.cn/database/redis/redis-memory-fragmentation.html#如何清理-redis-内存碎片)

Redis4.0-RC3 版本以后自带了内存整理，可以避免内存碎片率过大的问题。

直接通过 `config set` 命令将 `activedefrag` 配置项设置为 `yes` 即可。

```bash
config set activedefrag yes
```

具体什么时候清理需要通过下面两个参数控制：

```bash
# 内存碎片占用空间达到 500mb 的时候开始清理
config set active-defrag-ignore-bytes 500mb
# 内存碎片率大于 1.5 的时候开始清理
config set active-defrag-threshold-lower 50
```

通过 Redis 自动内存碎片清理机制可能会对 Redis 的性能产生影响，我们可以通过下面两个参数来减少对 Redis 性能的影响：

```bash
# 内存碎片清理所占用 CPU 时间的比例不低于 20%
config set active-defrag-cycle-min 20
# 内存碎片清理所占用 CPU 时间的比例不高于 50%
config set active-defrag-cycle-max 50
```

另外，重启节点可以做到内存碎片重新整理。如果你采用的是高可用架构的 Redis 集群的话，你可以将碎片率过高的主节点转换为从节点，以便进行安全重启

# [Redis 生产问题（重要）](https://javaguide.cn/database/redis/redis-questions-02.html#redis-生产问题-重要)

## [缓存穿透](https://javaguide.cn/database/redis/redis-questions-02.html#缓存穿透)

### [什么是缓存穿透？](https://javaguide.cn/database/redis/redis-questions-02.html#什么是缓存穿透)

大量请求key不合理，根本不在缓存中，也不在数据库中

<img src="http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240430144947491.png" alt="image-20240430144947491"  />

### [缓存穿透的解决方案]()

最基本：做好参数校验，一些不合法的参数请求直接抛出异常信息返回给客户端。

>  比如查询的数据库 id 不能小于 0、传入的邮箱格式不对的时候直接返回错误消息给客户端等等。

:one:缓存无效key

如果缓存和数据库都查不到某个 key 的数据就写一个到 Redis 中去并设置过期时间，具体命令如下：`SET key value EX 10086` 。

- 应对请求key变化不频繁的情况
- 每次都是不同key，会导致缓存大量无效key
- 不能根本解决问题，如果采用，将无效key过期时间设置短点

:two:布隆过滤器（Bloom Filter）

视作二进制向量（位数组）与随机映射函数（hash函数）组成

优点：比于我们平时常用的 List、Map、Set 等数据结构，它占用空间更少并且效率更高

缺点：

- 返回结果是概率，并不是十分准确，添加到集合越多，越容易错误
- 存放的数据不易删除

![image-20240430145505213](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240430145505213.png)

![image-20240430150030762](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240430150030762.png)

:three:接口限流

根据用户或者 IP 对接口进行限流，对于异常频繁的访问行为，还可以采取黑名单机制，例如将异常 IP 列入黑名单

## [缓存击穿](https://javaguide.cn/database/redis/redis-questions-02.html#缓存击穿)

### [什么是缓存击穿？](https://javaguide.cn/database/redis/redis-questions-02.html#什么是缓存击穿)

缓存击穿中，请求的 key 对应的是 **热点数据** ，该数据 **存在于数据库中，但不存在于缓存中（通常是因为缓存中的那份数据已经过期）** 。这就可能会导致瞬时大量的请求直接打到了数据库上，对数据库造成了巨大的压力，可能直接就被这么多请求弄宕机了。

![image-20240430150057248](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240430150057248.png)

### [缓存击穿的解决方案]()

1. 设置热点数据永不过期或者过期时间比较长。
2. 针对热点数据提前预热，将其存入缓存中并设置合理的过期时间比如秒杀场景下的数据在秒杀结束之前不过期。
3. 请求数据库写数据到缓存之前，先获取互斥锁，保证只有一个请求会落到数据库上，减少数据库的压力。
4. 维护逻辑时间，在业务代码中判断是否过期，过期之后再去数据库取数据（持有互斥锁）

## [缓存穿透和缓存击穿有什么区别？](https://javaguide.cn/database/redis/redis-questions-02.html#缓存穿透和缓存击穿有什么区别)

- 缓存穿透中，请求的 key 既不存在于缓存中，也不存在于数据库中。

- 缓存击穿中，请求的 key 对应的是 **热点数据** ，该数据 **存在于数据库中，但不存在于缓存中（通常是因为缓存中的那份数据已经过期）** 。

## [缓存雪崩](https://javaguide.cn/database/redis/redis-questions-02.html#缓存雪崩)

### [什么是缓存雪崩？](https://javaguide.cn/database/redis/redis-questions-02.html#什么是缓存雪崩)

实际上，缓存雪崩描述的就是这样一个简单的场景：**缓存在同一时间大面积的失效，导致大量的请求都直接落到了数据库上，对数据库造成了巨大的压力。** 这就好比雪崩一样，摧枯拉朽之势，数据库的压力可想而知，可能直接就被这么多请求弄宕机了。

另外，缓存服务宕机也会导致缓存雪崩现象，导致所有的请求都落到了数据库上。

![image-20240430152752802](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240430152752802.png)

### [缓存雪崩的解决方法]()

**针对 Redis 服务不可用的情况：**

1. 采用 Redis 集群，避免单机出现问题整个缓存服务都没办法使用。
2. 限流，避免同时处理大量的请求。
3. 多级缓存，例如本地缓存+Redis 缓存的组合，当 Redis 缓存出现问题时，还可以从本地缓存中获取到部分数据。

**针对热点缓存失效的情况：**

1. 设置不同的失效时间比如随机设置缓存的失效时间。
2. 缓存永不失效（不太推荐，实用性太差）。
3. 缓存预热，也就是在程序启动后或运行过程中，主动将热点数据加载到缓存中。

**缓存预热如何实现？**

常见的缓存预热方式有两种：

1. 使用定时任务，比如 xxl-job，来定时触发缓存预热的逻辑，将数据库中的热点数据查询出来并存入缓存中。
2. 使用消息队列，比如 Kafka，来异步地进行缓存预热，将数据库中的热点数据的主键或者 ID 发送到消息队列中，然后由缓存服务消费消息队列中的数据，根据主键或者 ID 查询数据库并更新缓存。

## [缓存雪崩和缓存击穿有什么区别？](https://javaguide.cn/database/redis/redis-questions-02.html#缓存雪崩和缓存击穿有什么区别)

缓存雪崩导致的原因是缓存中的==大量或者所有数据失效==

缓存击穿导致的原因主要是==某个热点数据==不存在与缓存中（通常是因为缓存中的那份数据已经过期）

## [如何保证缓存和数据库数据的一致性？](https://javaguide.cn/database/redis/redis-questions-02.html#如何保证缓存和数据库数据的一致性)

Cache Aside Pattern 中遇到写请求是这样的：更新数据库，然后直接删除缓存 。

如果更新数据库成功，而删除缓存这一步失败的情况的话，简单说有两个解决方案：

1. **缓存失效时间变短（不推荐，治标不治本）**：我们让缓存数据的过期时间变短，这样的话缓存就会从数据库中加载数据。另外，这种解决办法对于先操作缓存后操作数据库的场景不适用。
2. **增加缓存更新重试机制（常用）**：如果缓存服务当前不可用导致缓存删除失败的话，我们就隔一段时间进行重试，重试次数可以自己定。不过，这里更适合引入消息队列实现异步重试，将删除缓存重试的消息投递到消息队列，然后由专门的消费者来重试，直到成功。虽然说多引入了一个消息队列，但其整体带来的收益还是要更高一些。

相关文章推荐：[缓存和数据库一致性问题，看这篇就够了 - 水滴与银弹](https://mp.weixin.qq.com/s?__biz=MzIyOTYxNDI5OA==&mid=2247487312&idx=1&sn=fa19566f5729d6598155b5c676eee62d&chksm=e8beb8e5dfc931f3e35655da9da0b61c79f2843101c130cf38996446975014f958a6481aacf1&scene=178&cur_album_id=1699766580538032128#rd)

## [哪些情况可能会导致 Redis 阻塞？](https://javaguide.cn/database/redis/redis-questions-02.html#哪些情况可能会导致-redis-阻塞)

### [O(n) 命令](https://javaguide.cn/database/redis/redis-common-blocking-problems-summary.html#o-n-命令)

Redis 中的大部分命令都是 O(1)时间复杂度，但也有少部分 O(n) 时间复杂度的命令：

- `KEYS *`：会返回所有符合规则的 key。
- `HGETALL`：会返回一个 Hash 中所有的键值对。
- `LRANGE`：会返回 List 中指定范围内的元素。
- `SMEMBERS`：返回 Set 中的所有元素。
- `SINTER`/`SUNION`/`SDIFF`：计算多个 Set 的交集/并集/差集。

这些命令并不是一定不能使用，但是需要明确 N 的值。另外，有遍历的需求可以使用 `HSCAN`、`SSCAN`、`ZSCAN` 代替（他们可以一次获得一部分，而不是一下子全部获取完）

复杂度可能高于O(n)的指令：

- `ZRANGE`/`ZREVRANGE`：返回指定 Sorted Set 中指定排名范围内的所有元素。时间复杂度为 O(log(n)+m)，n 为所有元素的数量， m 为返回的元素数量，当 m 和 n 相当大时，O(n) 的时间复杂度更小。
- `ZREMRANGEBYRANK`/`ZREMRANGEBYSCORE`：移除 Sorted Set 中指定排名范围/指定 score 范围内的所有元素。时间复杂度为 O(log(n)+m)，n 为所有元素的数量， m 被删除元素的数量，当 m 和 n 相当大时，O(n) 的时间复杂度更小。

### [SAVE 创建 RDB 快照](#save-创建-rdb-快照)

Redis 提供了两个命令来生成 RDB 快照文件：

- `save` : 同步保存操作，会阻塞 Redis 主线程；
- `bgsave` : fork 出一个子进程，子进程执行，不会阻塞 Redis 主线程，默认选项。

默认情况下，Redis 默认配置会使用 `bgsave` 命令。如果手动使用 `save` 命令生成 RDB 快照文件的话，就会阻塞主线程

### [AOF](https://javaguide.cn/database/redis/redis-common-blocking-problems-summary.html#aof)

#### [AOF 日志记录阻塞](https://javaguide.cn/database/redis/redis-common-blocking-problems-summary.html#aof-日志记录阻塞)

Redis AOF 持久化机制是在执行完命令之后再记录日志，这和关系型数据库（如 MySQL）通常都是执行命令之前记录日志（方便故障恢复）不同。

**为什么是在执行完命令之后记录日志呢？**

- 避免额外的检查开销，AOF 记录日志不会对命令进行语法检查；
- 在命令执行完之后再记录，不会阻塞当前的命令执行。

这样也带来了风险（我在前面介绍 AOF 持久化的时候也提到过）：

- 如果刚执行完命令 Redis 就宕机会导致对应的修改丢失；
- **可能会阻塞后续其他命令的执行（AOF 记录日志是在 Redis 主线程中进行的）**。

#### [AOF 刷盘阻塞](https://javaguide.cn/database/redis/redis-common-blocking-problems-summary.html#aof-刷盘阻塞)

开启 AOF 持久化后每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到 AOF 缓冲区 `server.aof_buf` 中，然后再根据 `appendfsync` 配置来决定何时将其同步到硬盘中的 AOF 文件。

在 Redis 的配置文件中存在三种不同的 AOF 持久化方式（ `fsync`策略），它们分别是：

1. `appendfsync always`：主线程调用 `write` 执行写操作后，后台线程（ `aof_fsync` 线程）立即会调用 `fsync` 函数同步 AOF 文件（刷盘），`fsync` 完成后线程返回，这样会严重降低 Redis 的性能（`write` + `fsync`）。
2. `appendfsync everysec`：主线程调用 `write` 执行写操作后立即返回，由后台线程（ `aof_fsync` 线程）每秒钟调用 `fsync` 函数（系统调用）同步一次 AOF 文件（`write`+`fsync`，`fsync`间隔为 1 秒）
3. `appendfsync no`：主线程调用 `write` 执行写操作后立即返回，让操作系统决定何时进行同步，Linux 下一般为 30 秒一次（`write`但不`fsync`，`fsync` 的时机由操作系统决定）。

当后台线程（ `aof_fsync` 线程）调用 `fsync` 函数同步 AOF 文件时，需要等待，直到写入完成。当磁盘压力太大的时候，会导致 `fsync` 操作发生阻塞，主线程调用 `write` 函数时也会被阻塞。`fsync` 完成后，主线程执行 `write` 才能成功返回。

#### [AOF 重写阻塞](#aof-重写阻塞)

1. fork 出一条子线程来将文件重写，在执行 `BGREWRITEAOF` 命令时，Redis 服务器会维护一个 AOF 重写缓冲区，该缓冲区会在子线程创建新 AOF 文件期间，记录服务器执行的所有写命令。
2. 当子线程完成创建新 AOF 文件的工作之后，服务器会将重写缓冲区中的所有内容追加到新 AOF 文件的末尾，使得新的 AOF 文件保存的数据库状态与现有的数据库状态一致。
3. 最后，服务器用新的 AOF 文件替换旧的 AOF 文件，以此来完成 AOF 文件重写操作。

阻塞就是出现在第 2 步的过程中，将缓冲区中新数据写到新文件的过程中会产生**阻塞**。

### [bigkey]()

阻塞问题：

- 客户端超时阻塞：由于 Redis 执行命令是单线程处理，然后在操作大 key 时会比较耗时，那么就会阻塞 Redis，从客户端这一视角看，就是很久很久都没有响应。
- 引发网络阻塞：每次获取大 key 产生的网络流量较大，如果一个 key 的大小是 1 MB，每秒访问量为 1000，那么每秒会产生 1000MB 的流量，这对于普通千兆网卡的服务器来说是灾难性的。
- 阻塞工作线程：如果使用 del 删除大 key 时，会阻塞工作线程，这样就没办法处理后续的命令

查找时：当我们在使用 Redis 自带的 `--bigkeys` 参数查找大 key 时，最好选择在从节点上执行该命令，因为主节点上执行时，会**阻塞**主节点。

删除时：

- 在应用程序释放内存时，**操作系统需要把释放掉的内存块插入一个空闲内存块的链表**，以便后续进行管理和再分配。这个过程本身需要一定时间，而且会**阻塞**当前释放内存的应用程序。
- 所以，如果一下子释放了大量内存，空闲内存块链表操作时间就会增加，相应地就会造成 Redis 主线程的阻塞，如果主线程发生了阻塞，其他所有请求可能都会超时，超时越来越多，会造成 Redis 连接耗尽，产生各种异常。

### [清空数据库](https://javaguide.cn/database/redis/redis-common-blocking-problems-summary.html#清空数据库)

清空数据库和上面 bigkey 删除也是同样道理，`flushdb`、`flushall` 也涉及到删除和释放所有的键值对，也是 Redis 的阻塞点。

### [集群扩容](#集群扩容)

Redis 集群可以进行节点的动态扩容缩容，这一过程目前还处于半自动状态，需要人工介入。

在扩缩容的时候，需要进行数据迁移。而 Redis 为了保证迁移的一致性，迁移所有操作都是同步操作。

执行迁移时，两端的 Redis 均会进入时长不等的阻塞状态，对于小 Key，该时间可以忽略不计，但如果一旦 Key 的内存使用过大，严重的时候会触发集群内的故障转移，造成不必要的切换。

### [Swap（内存交换）](#swap-内存交换)

Linux 中的 Swap 常被称为内存交换或者交换分区。类似于 Windows 中的虚拟内存，就是当内存不足的时候，把一部分硬盘空间虚拟成内存使用，从而解决内存容量不足的情况。因此，Swap 分区的作用就是牺牲硬盘，增加内存，解决 VPS 内存不够用或者爆满的问题。

Swap 对于 Redis 来说是非常致命的，Redis 保证高性能的一个重要前提是所有的数据在内存中。如果操作系统把 Redis 使用的部分内存换出硬盘，由于内存与硬盘的读写速度差几个数量级，会导致发生交换后的 Redis 性能急剧下降。

识别 Redis 发生 Swap 的检查方法如下：

1、查询 Redis 进程号

```bash
redis-cli -p 6383 info server | grep process_id
process_id: 4476
```

2、根据进程号查询内存交换信息

```bash
cat /proc/4476/smaps | grep Swap
Swap: 0kB
Swap: 0kB
Swap: 4kB
Swap: 0kB
Swap: 0kB
.....
```

如果交换量都是 0KB 或者个别的是 4KB，则正常。

预防内存交换的方法：

- 保证机器充足的可用内存
- 确保所有 Redis 实例设置最大可用内存(maxmemory)，防止极端情况 Redis 内存不可控的增长
- 降低系统使用 swap 优先级，如`echo 10 > /proc/sys/vm/swappiness`

### [CPU 竞争](#cpu-竞争)

Redis 是典型的 CPU 密集型应用，不建议和其他多核 CPU 密集型服务部署在一起。当其他进程过度消耗 CPU 时，将严重影响 Redis 的吞吐量。

可以通过`redis-cli --stat`获取当前 Redis 使用情况。通过`top`命令获取进程对 CPU 的利用率等信息 通过`info commandstats`统计信息分析出命令不合理开销时间，查看是否是因为高算法复杂度或者过度的内存优化问题。

### [网络问题](https://javaguide.cn/database/redis/redis-common-blocking-problems-summary.html#网络问题)

连接拒绝、网络延迟，网卡软中断等网络问题也可能会导致 Redis 阻塞。

# [Redis主从复制]()

## [什么是主从复制？]()

将一台主节点的数据复制到其他从节点中，保证主从数据一致。

主从一致是redis高可用的基石

**主从复制这种方案不仅保障了 Redis 服务的高可用，还实现了读写分离，提高了系统的并发量，尤其是读并发量。**

主从复制这种方案基于 [Redis replication](https://redis.io/docs/manual/replication/)（默认使用异步复制），开发者可以通过 replicaof （Redis 5.0 之前是 slaveof 命令）命令来配置各个 Redis 节点的主从关系。

```bash
# 在指定 redis 节点上执行 replicaof 命令就可以让其成为 master 的 slave
# ip port 是 master 的
replicaof ip port
```

配置完成之后，主从节点之间的数据同步会自动进行，不需要人为插手。

至于具体要配置多少 slave 节点，主要取决于项目的读吞吐量，因为 slave 节点分担的是读请求，写请求由 master 节点负责。

## [**主从复制下从节点会主动删除过期数据吗？**]()

> 类似问题：
>
> - 主从复制下会读取到过期的数据么
> - 主从复制下如何避免读到过期的数据

Redis 中常用的过期数据的删除策略就两个（重要！自己造缓存轮子的时候需要格外考虑的东西）：

- 惰性删除 ：只会在取出 key 的时候才对数据进行过期检查。这样对 CPU 最友好，但是可能会造成太多过期 key 没有被删除。
- 定期删除 ： 每隔一段时间抽取一批 key 执行删除过期 key 操作。并且，Redis 底层会通过限制删除操作执行的时长和频率来减少删除操作对 CPU 时间的影响。

那客户端读取从节点会读取到过期数据么？ 答案是：有可能，但要看具体的情况。

- Redis 3.2 版本之前，客户端读从库并不会判断数据是否过期，有可能返回过期数据。Redis 3.2 版本及之后，客户端读从库会先判断数据是否过期，如果过期的话，就会删除对应的数据并返回空值。
- 采用 EXPIRE 或者PEXPIRE设置过期时间的话，表示的是从执行这个命令开始往后 TTL 时间过期。如果从节点同步执行命令因为网络等原因延迟的话，客户端就可能会读取到过期的数据（如下图所示，客户端在 T3-T4 之间读取到的就是过期数据）。这种情况可以考虑使用 EXPIREAT 和 PEXPIREAT，这两个命令语义和效果和 EXPIRE 或者PEXPIRE类似，但不是指定 TTL（生存时间）的秒数/毫秒数，而是使用绝对的 [Unix 时间戳](http://en.wikipedia.org/wiki/Unix_time)（自 1970 年 1 月 1 日以来的秒数）。由于设置的是时间点，主从节点的时钟需要保持一致

![image-20240506160218087](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240506160218087.png)

## [**主从节点之间如何同步数据？**]()

> 类似的问题：
>
> - 主从复制的原理是什么？
> - master 节点的数据是如何同步给 slave 节点的?
> - 复制积压缓冲区的有什么用?

### [Redis 2.8 之前的 SYNC 方案]()

Redis 在 2.8 版本之前都是基于 [SYNC](https://redis.io/commands/sync/) 命令执行全量同步，整个步骤简化后是这样的：

1. slave 向 master 发送 SYNC 命令请求启动复制流程；
2. master 收到 SYNC 命令之后执行 [BGSAVE](https://redis.io/commands/bgsave/) 命令（子线程执行，不会阻塞主线程）生成 RDB 文件（dump.rdb）；
3. master 将生成的 RDB 文件发送给 slave；
4. slave 收到 RDB 文件之后就开始加载解析 RDB 同步更新本地数据；
5. 更新完成之后，slave 的状态相当于是 master 执行 BGSAVE 命令时的状态。master 会将 BGSAVE 命令之后接受的写命令缓存起来，因为这部分写命令 slave 还未同步；
6. master 将自己缓存的这些写命令发送给 slave，slave 执行这些写命令同步 master 的最新状态；
7. slave 到这个时候已经完成全量复制，后续会通过和 master 维护的长连接来进行命令传播，同步最新的写命令。

![image-20240506160820363](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240506160820363.png)

master 为每一个 slave 单独 开辟一块 replication buffer（复制缓存区）来记录 RDB 文件生成后 master 收到的所有写命令。复制缓冲区参数超出阈值后，会起强制断开slave连接

```c
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
```

虽然 BGSAVE 子进程写入 RDB 的工作不会阻塞主线程，但会对机器的 CPU 资源和内存资源产生影响，严重的情况下甚至会直接把 Redis 服务干宕机。

- 写入 RDB 的过程中，为了避免影响写操作，主线程修改的内存数据会被复制一份副本，BGSAVE 子进程把这个副本数据写入 RDB 文件。如果修改内存数据的请求比较多的话，生成内存数据副本产生的内存消耗是非常大的。这个过程称为 Copy On Write（写时复制，COW） ，操作系统层面提供的机制。
- 大量的写时复制会产生大量的分页错误（也叫缺页中断、页缺失），消耗大量的 CPU 资源。

存在问题：

- BGSAVE属于重量级操作，会对机器的CPU和内存资源产生影响。

- slave 加载 RDB 的过程中不能对外提供读服务。
- slave 和 master 断开连接之后，slave 重新连上 master 需要重新进行全量同步。

### [**Redis 2.8 PSYNC 方案**]()

Redis 2.8 版本 SYNC 命令被 [PSYNC](https://redis.io/commands/psync/) 取代，PSYNC 格式如下（相比较于 SYNC 命令，多了两个参数）：

```bash
PSYNC replicationid offset
```

PSYNC 解决了 slave 和 master 断开连接之后需要重新进行全量同步的问题。不过，部分情况（比如 slave 突然宕机或者被重启）重连之后依然需要进行全量同步。

slave 会记录 master 的运行 id （也就是 runid, 节点启动时生成的40字节随机字符串）和自己的复制进度/偏移量（slave_repl_offset）。

master 也会记录自己写入缓冲区的偏移量（master_repl_offset），如果 runid 匹配的话，通过 slave_repl_offset 和 master_repl_offset 就可以确认 slave 缺少的数据是否在缓冲区中以及缺少的具体是哪一部分的数据。

![image-20240506171430253](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240506171430253.png)

首次连接：

![image-20240506171445964](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240506171445964.png)

master 通过一个环形的 复制积压缓冲区（repl_backlog_buffer） 来记录从生成 RDB 文件开始收到的所有写命令。一个 master 中只有一个复制积压缓冲区，master 所有的 slave 共用

![image-20240506172003270](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240506172003270.png)

repl_backlog_buffer 是一个固定长度的队列（FIFO，先进先出），默认为 1MB 大小，支持自定义大小。

repl_backlog_buffer 的信息在 Replication 块里，你可以通过 INFO replication 命令查看。

如果 slave 缺少的那部分数据存在这个队列中，可以进行增量同步。否则的话， 还是要进行全量同步操作。

全量同步的情况：

- slave 突然宕机或者被重启， runid、offset 都丢失了
- master 突然宕机，新选出来的 master 的 runid 和 offset 都会发生变化。

### [Redis 4.0 PSYNC2.0 方案]()

优化PSYNC，发生主从切换，依然有可能进行增量同步而不是必须全量同步

放弃使用`runid`，使用`replid`和`replid2`

- 对于 master 来说，`replid `就是自己的复制 id。没有发生主从切换之前，`replid2`为空。发生主从切换之后，新的 master 的 `replid2`是旧 master （前一个自己同步的 master） 的 `replid`，在主从角色切换的时候会用到。
- 对于 slave 来说，`replid `保存的是自己当前正在同步的 master 的 `replid`。`replid2`保存的是旧 master 的 `replid`，在主从角色切换的时候会用到。
- `master_repl_offset `: 当前的复制偏移量。
- `second_replid_offset `：没有发生主从切换之前，`second_replid_offset`的值为 -1。发生主从切换之后，新的 master 的 `second_replid_offset`是旧 master 的复制偏移量。

`replid `和 `replid2`用于判断发生主从切换之后，新的 master 和 slave 曾经属于同一个主库。如果属于同一个主库，可以进行增量同步的尝试。

master 的同步进度必须比 slave 更快并且两者的同步进度必须在规定范围，不能超过 repl_backlog 的大小，否则的话，还是要进行全量同步操作。

另外，为了解决 slave 在重启之后需要跟 master 全量同步的问题，RDB 会记录主从复制相关的信息（比如 replid）

## [**为什么主从全量复制使用 RDB 而不是 AOF？**]()

:one:

- RDB 文件存储的内容是经过压缩的二进制数据，文件很小。
- AOF 文件存储的是每一次写命令，类似于 MySQL 的 binlog 日志，通常会必 RDB 文件大很多
- 传输 RDB 文件更节省带宽，速度也更快

:two:

- 使用 RDB 文件恢复数据，直接解析还原数据即可，不需要一条一条地执行命令，速度非常快。
-  AOF 则需要依次执行每个写命令，速度非常慢。
- 与 AOF 相比，恢复大数据集的时候，RDB 速度更快

:three:

- AOF 需要选择合适的刷盘策略，如果刷盘策略选择不当的话，会影响 Redis 的正常运行。并且，根据所使用的刷盘策略，AOF 的速度可能会慢于 RDB。

> RBD的丢数据问题？

## [**主从复制方案有什么痛点？**]()

- mater宕机，不使用哨兵，则需要手动选择新节点
- 高并发场景下能力有限，缓存数据量过大或并发要求高，无法满足要求
- 主从复制+哨兵不能横向扩展缓解写压力和缓存数据量过大的问题

# [**Redis Sentinel：如何实现自动化地故障转移？**]()

## [什么是 Sentinel？]()

Sentinel（哨兵） 只是 Redis 的一种运行模式 ，不提供读写服务，默认运行在 26379 端口上，依赖于 Redis 工作。Redis Sentinel 的稳定版本是在 Redis 2.8 之后发布的。

## [**Sentinel 有什么作用？**]()

- 监控：监控所有 redis 节点（包括 sentinel 节点自身）的状态是否正常。
- 故障转移：如果一个 master 出现故障，Sentinel 会帮助我们实现故障转移，自动将某一台 slave 升级为 master，确保整个 Redis 系统的可用性。
- 通知 ：通知 slave 新的 master 连接信息，让它们执行 replicaof 成为新的 master 的 slave。
- 配置提供 ：客户端连接 sentinel 请求 master 的地址，如果发生故障转移，sentinel 会通知新的 master 链接信息给客户端

## [Sentinel 如何检测节点是否下线？]()

> 相关的问题：
>
> - 主观下线与客观下线的区别?
> - Sentinel 是如何实现故障转移的？
> - 为什么建议部署多个 sentinel 节点（哨兵集群）？

- 主观下线(SDOWN) ：sentinel 节点认为某个 Redis 节点已经下线了（主观下线），但还不是很确定，需要其他 sentinel 节点的投票。
- 客观下线(ODOWN) ：法定数量（通常为过半）的 sentinel 节点认定某个 Redis 节点已经下线（客观下线），那它就算是真的下线了

每个 sentinel 节点以每秒钟一次的频率向整个集群中的 master、slave 以及其他 sentinel 节点发送一个 PING 命令。

如果对应的节点超过规定的时间（down-after-millseconds）没有进行有效回复的话，就会被其认定为是 主观下线(SDOWN) 。

> 注意！这里的有效回复不一定是 PONG，可以是-LOADING 或者 -MASTERDOWN 。

- slave主观下线什么都不会发生
- master主观下线，进一步核实（当法定数量（通常为过半）的 sentinel 节点认定 master 已经下线， master 才被判定为 客观下线(ODOWN)）

 sentinel 中会有一个 Leader 的角色来负责故障转移，也就是自动地从 slave 中选出一个新的 master 并执行完相关的一些工作(比如通知 slave 新的 master 连接信息，让它们执行 replicaof 成为新的 master 的 slave)。

如果没有足够数量的 sentinel 节点认定 master 已经下线的话，当 master 能对 sentinel 的 PING 命令进行有效回复之后，master 也就不再被认定为主观下线，回归正常。

## [**Sentinel 如何选择出新的 master?**]()

slave 必须是在线状态才能参加新的 master 的选举，筛选出所有在线的 slave 之后，通过下面 3 个维度进行最后的筛选（优先级依次降低）

- ==slave 优先级== ：可以通过修改 slave-priority（redis.conf中配置，Redis5.0 后该配置属性名称被修改为 replica-priority） 配置的值来手动设置 slave 的优先级。

  > slave-priority默认值为 100，其值越小得分越高，越有机会成为 master。0 是一个特殊的优先级值 ，代表其没有参加 master 选举的资格。如果没有优先级最高的，再判断复制进度。

- ==复制进度== ：Sentinel 总是希望选择出数据最完整（与旧 master 数据最接近）也就是复制进度最快的 slave 被提升为新的 master，复制进度越快得分也就越高。

- ==runid(运行 id)== ：通常经过前面两轮筛选已经成功选出来了新的 master，万一真有多个 slave 的优先级和复制进度一样的话，那就 runid 小的成为新的 master，每个 redis 节点启动时都有一个 40 字节随机字符串作为运行 id。

## [**如何从 Sentinel 集群中选择出 Leader ？**]()

采用分布式领域的共识算法，Raft算法，是Multi-Paxos的变种

## [**Sentinel 可以防止脑裂吗？**]()

![image-20240507162545916](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240507162545916.png)

M1 和 R2、R3 之间的网络被隔离，也就是发生了脑裂，M1 和 R2 、 R3 隔离在了两个不同的网络分区中。这意味着，R2 或者 R3 其中一个会被选为 master，这里假设为 R2。

如果客户端 C1 是和 M1 在一个网络分区的话，从网络被隔离到网络分区恢复这段时间，C1 写入 M1 的数据都会丢失，并且，C1 读取的可能也是过时的数据。这是因为当网络分区恢复之后，M1 将会成为 slave 节点。

进行如下配置解决：

- `min-replicas-to-write 1`：用于配置写 master 至少写入的 slave 数量，设置为 0 表示关闭该功能。3 个节点的情况下，可以配置为 1 ，表示 master 必须写入至少 1 个 slave ，否则就停止接受新的写入命令请求。
- `min-replicas-max-lag 10 `：用于配置 master 多长时间（秒）无法得到从节点的响应，就认为这个节点失联。我们这里配置的是 10 秒，也就是说 master 10 秒都得不到一个从节点的响应，就会认为这个从节点失联，停止接受新的写入命令请求。

不过，这样配置会降低 Redis 服务的整体可用性，如果 2 个 slave 都挂掉，master 将会停止接受新的写入命令请求。

# [**Redis Cluster：缓存的数据量太大怎么办？**]()

## [为什么需要Redis Cluster](#test)

1. 缓存的数据量太大 ：实际缓存的数据量可以达到几十 G，甚至是成百上千 G；
2. 并发量要求太大 ：号称10w并发，但实际中不可靠更多，复杂读写会影响并发。而且10w可能不够

Redis 切片集群 就是部署多台 Redis 主节点（master），这些节点之间平等，并没有主从之说，同时对外提供读/写服务。缓存的数据库相对均匀地分布在这些 Redis 实例上，客户端的请求通过路由规则转发到目标 master 上。

![image-20240507165816427](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240507165816427.png)

通过对每个master配置slave来保障高可用

对横向拓展非常友好，**只需要增加 Redis 节点到集群中即可**

Redis Cluster 通过 分片（Sharding） 来进行数据管理，提供 主从复制（Master-Slave Replication）、故障转移（Failover） 等开箱即用的功能，可以非常方便地帮助我们解决 Redis 大数据量缓存以及 Redis 服务高可用的问题。

Redis Cluster 的主要优势：

- 可以横向扩展缓解写压力和存储压力，支持动态扩容和缩容；
- 具备主从复制、故障转移（内置了 Sentinel 机制，无需单独部署 Sentinel 集群）等开箱即用的功能。

## [一个最基本的 Redis Cluster 架构是怎样的？]()

为了保证高可用，Redis Cluster 至少需要 3 个 master 以及 3 个 slave，也就是说每个 master 必须有 1 个 slave。master 和 slave 之间做主从复制，slave 会实时同步 master 上的数据。

不同于普通的 Redis 主从架构，这里的 slave 不对外提供读服务，主要用来保障 master 的高可用，当 master 出现故障的时候替代它。

- 一个slave，master挂了，替代
- 多个slave，master挂了，选一个数据最完整的

去中心化，使用Gossip进行通信

如果宕机的 master 无 slave 的话，为了保障集群的完整性，保证所有的哈希槽都指派给了可用的 master ，整个集群将不可用。

> 这种情况下，还是想让集群保持可用的话，可以将cluster-require-full-coverage 这个参数设置成 no，cluster-require-full-coverage 表示需要 16384 个 slot 都正常被分配的时候 Redis Cluster 才可以对外提供服务

## [**Redis Cluster 是如何分片的？**]()

> 类似的问题：
>
> - Redis Cluster 中的数据是如何分布的？
> - 如何确定给定 key 的应该分布到哪个哈希槽中？

Redis Cluster 并没有使用一致性哈希，采用的是 哈希槽分区 ，每一个键值对都属于一个 hash slot（哈希槽）

Redis Cluster 通常有 16384 个哈希槽 ，通过对每个 key 计算 CRC-16（XMODEM） 校验码，然后再对这个校验码对 16384(哈希槽的总数) 取模，得到的值即是 key 对应的哈希槽。

创建并初始化 Redis Cluster 的时候，Redis 会自动平均分配这 16384 个哈希槽到各个节点，不需要我们手动分配

在任意一个 master 节点上执行 `CLUSTER SLOTS`命令即可返回哈希槽和节点的映射关系

![image-20240507171439753](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240507171439753.png)

可能产生重定向问题，因为redis内部重新分配哈希槽（哈希扩容缩容的时候），可能会导致客户端缓存的哈希槽分配信息有误，此时会返回`-MOVED`，重定向错误来进行重定向。

![image-20240507171614187](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240507171614187.png)

优点：**解耦了数据和节点之间的关系，提升了集群的横向扩展性和容错性。**

## [**为什么 Redis Cluster 的哈希槽是 16384 个?**]()

CRC16 算法产生的校验码有 16 位，理论上可以产生 65536（2^16，0 ~ 65535）个值。为什么 Redis Cluster 的哈希槽偏偏选择的是 16384（2^14）个呢？

原因：

- 正常的心跳包（ping pong）会携带一个节点的完整配置，它会以幂等的方式更新旧的配置，这意味着心跳包会附带当前节点的负责的哈希槽的信息。假设哈希槽采用 16384 ,则占空间 2k(16384/8)。假设哈希槽采用 65536， 则占空间 8k(65536/8)，这是令人难以接受的内存占用。
- 由于其他设计上的权衡，Redis Cluster 不太可能扩展到超过 1000 个主节点，够用了。
- 哈希槽总数越少，对存储哈希槽信息的 bitmap 压缩效果越好；

实际上运用bitmap维护哈希槽信息，实际传输过程中会进行压缩，bitmap 的填充率越低，压缩率越高。bitmap 的填充率的值是 哈希槽总数/节点数 ，如果哈希槽总数太大的话，bitmap 的填充率的值也会比较大。

> :question:填充率到底和这个有什么关系

## [Redis Cluster 如何重新分配哈希槽？]()

手动分配指令：

- `CLUSTER ADDSLOTS slot [slot ...] `: 把一组 hash slots 分配给接收命令的节点，时间复杂度为 O(N)，其中 N 是 hash slot 的总数；
- `CLUSTER ADDSLOTSRANGE start-slot end-slot [start-slot end-slot ...]` （Redis 7.0 后新加的命令）： 把指定范围的 hash slots 分配给接收命令的节点，类似于 ADDSLOTS 命令，时间复杂度为 O(N) 其中 N 是起始 hash slot 和结束 hash slot 之间的 hash slot 的总数。
- `CLUSTER DELSLOTS slot [slot ...] `: 从接收命令的节点中删除一组 hash slots；
- `CLUSTER FLUSHSLOTS `：移除接受命令的节点中的所有 hash slot；
- `CLUSTER SETSLOT slot MIGRATING node-id`： 迁移接受命令的节点的指定 hash slot 到目标节点（node_id 指定）中；
- `CLUSTER SETSLOT slot IMPORTING node-id`： 将目标节点（node_id 指定）中的指定 hash slot 迁移到接受命令的节点中；

## [**Redis Cluster 扩容缩容期间可以提供服务吗？**]()

>  类似的问题：
>
> - 如果客户端访问的 key 所属的槽正在迁移怎么办？
> - 如何确定给定 key 的应该分布到哪个哈希槽中？

**Redis Cluster 扩容和缩容本质是进行重新分片，动态迁移哈希槽。**

为了保证 Redis Cluster 在扩容和缩容期间依然能够对外正常提供服务，Redis Cluster 提供了重定向机制，两种不同的类型：

- ASK 重定向 ：可以看做是临时重定向，后续查询仍然发送到旧节点。
- MOVED 重定向 ：可以看做是永久重定向，后续查询发送到新节点。

ASK重定向：

1. 如果请求的 key 对应的哈希槽还在当前节点的话，就直接响应客户端的请求。
2. 如果请求的 key 对应的哈希槽在迁移过程中，但是请求的 key 还未迁移走的话，说明当前节点任然可以处理当前请求，同样可以直接响应客户端的请求。
3. 如果客户端请求的 key 对应的哈希槽当前正在迁移至新的节点且请求的 key  已经被迁移走的话，就会返回 `-ASK` 重定向错误，告知客户端要将请求发送到哈希槽被迁移到的目标节点。` -ASK` 重定向错误信息中包含请求 key 迁移到的新节点的信息。
4. 客户端收到 `-ASK` 重定向错误后，将会临时（一次性）重定向，自动向新节点发送一条 [ASKING](https://redis.io/commands/asking/) 命令。也就是说，接收到 ASKING 命令的节点会强制执行一次请求，下次再来需要重新提前发送 ASKING 命令。
5. 新节点在收到 ASKING 命令后可能会返回重试错误（TRYAGAIN），因为可能存在当前请求的 key 还在导入中但未导入完成的情况。
6. 客户端发送真正需要请求的命令。
7. ASK 重定向并不会同步更新客户端缓存的哈希槽分配信息，也就是说，客户端对正在迁移的相同哈希槽的请求依然会发送到旧节点而不是新节点。

![image-20240507202421528](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240507202421528.png)

完成迁移，返回 `-MOVED` 重定向错误，告知客户端当前哈希槽是由哪个节点负责，客户端向新节点发送请求并更新缓存的哈希槽分配信息，后续查询将被发送到新节点。

## [**Redis Cluster 中的节点是怎么进行通信的？**]()

各个节点基于 Gossip 协议 来进行通信共享信息，每个 Redis 节点都维护了一份集群的状态信息。

Redis Cluster 的节点之间会相互发送多种 Gossip 消息：

- `MEET` ：在 Redis Cluster 中的某个 Redis 节点上执行` CLUSTER MEET ip port `命令，可以向指定的 Redis 节点发送一条 MEET 信息，用于将其添加进 Redis Cluster 成为新的 Redis 节点。
- `PING/PONG `：Redis Cluster 中的节点都会定时地向其他节点发送 PING 消息，来交换各个节点状态信息，检查各个节点状态，包括在线状态、疑似下线状态 PFAIL 和已下线状态 FAIL。
- `FAIL` ：Redis Cluster 中的节点 A 发现 B 节点 PFAIL ，并且在下线报告的有效期限内集群中半数以上的节点将 B 节点标记为 PFAIL，节点 A 就会向集群广播一条 FAIL 消息，通知其他节点将故障节点 B 标记为 FAIL 。
- ......

有了 Redis Cluster 之后，不需要专门部署 Sentinel 集群服务了。Redis Cluster 相当于是内置了 Sentinel 机制，Redis Cluster 内部的各个 Redis 节点通过 Gossip 协议互相探测健康状态，在故障时可以自动切换。

# [Redis 使用规范](https://javaguide.cn/database/redis/redis-questions-02.html#redis-使用规范)

实际使用 Redis 的过程中，我们尽量要准守一些常见的规范，比如：

1. 使用连接池：避免频繁创建关闭客户端连接。
2. 尽量不使用 O(n)指令，使用 O(n) 命令时要关注 n 的数量：像 `KEYS *`、`HGETALL`、`LRANGE`、`SMEMBERS`、`SINTER`/`SUNION`/`SDIFF`等 O(n) 命令并非不能使用，但是需要明确 n 的值。另外，有遍历的需求可以使用 `HSCAN`、`SSCAN`、`ZSCAN` 代替。
3. 使用批量操作减少网络传输：原生批量操作命令（比如 `MGET`、`MSET`等等）、pipeline、Lua 脚本。
4. 尽量不适用 Redis 事务：Redis 事务实现的功能比较鸡肋，可以使用 Lua 脚本代替。
5. 禁止长时间开启 monitor：对性能影响比较大。
6. 控制 key 的生命周期：避免 Redis 中存放了太多不经常被访问的数据。
7. ……
