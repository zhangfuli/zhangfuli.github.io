---
title: Redis/Prometheus/RocketMQ篇-2021春招准备
date: 2021-03-01 17:14:17
tags:
---

#### 1、Redis

> 一个高性能的key-value非关系型数据库

##### 数据类型

- String
- List
- Set
- Hash
- ZSet: 有序集合



##### 过期策略以及内存淘汰机制

- 定期删除: 定时删除,用一个定时器来负责监视key,过期则自动删除。虽然内存及时释放，但是**十分消耗CPU资源**。在大并发请求下，CPU要将时间应用在处理请求，而不是删除key,因此没有采用这一策略.
- 定期删除+惰性删除
  - 定期删除: redis默认每个100ms检查，随机抽取进行检查
  - 惰性删除: 获取某个key的时候，redis会检查一下，这个key如果设置的过期时间(expire)，则删除
  - 内存淘汰机制: 如果定期删除没删除key。然后你也没即时去请求key，也就是说惰性删除也没生效。这样，redis的内存会越来越高。
    - allkeys-lru: 当内存不足时，在键空间中，移除最近最少使用的key
    - noeviction: 当内存不足时，新写入操作会报错
    - allkeys-random: 当内存不足时，在键空间中，随机移除某个key



##### redis和数据库双写一致性问题

一致性问题是分布式常见问题，还可以再分为最终一致性和强一致性。数据库和缓存双写，就必然会存在不一致的问题。如果对数据有强一致性要求，不能放缓存。

采取正确更新策略，先更新数据库，再删缓存。其次，因为可能存在删除缓存失败的问题，提供一个补偿措施即可，例如利用消息队列



##### 如何应对缓存穿透和缓存雪崩问题 

- 缓存击穿: 存的数据过期

- 缓存穿透：即黑客故意去请求缓存中不存在的数据，导致所有的请求都怼到数据库上，从而数据库连接异常。
  - 解决方案:
    - 利用互斥锁，缓存失效的时候，先去获得锁，得到锁了，再去请求数据库。没得到锁，则休眠一段时间重试
    - 采用异步更新策略，无论key是否取到值，都直接返回。异步起一个线程去读数据库，更新缓存。
    - 提供一个能迅速判断请求是否有效的拦截机制，比如，利用布隆过滤器(位图+多个hash)

- 缓存雪崩，即缓存同一时间大面积的失效，这个时候又来了一波请求，结果请求都怼到数据库上，从而导致数据库连接异常。
  - 解决方案:
    - 给缓存的失效时间，加上一个随机值，避免集体失效。
    - 使用互斥锁，但是该方案吞吐量明显下降了。
    - 双缓存。缓存A和缓存B。缓存A的失效时间为20分钟，缓存B不设失效时间。自己做缓存预热操作
      - 从缓存A读数据库，有则直接返回
      - A没有数据，直接从B读数据，直接返回，并且异步启动一个更新线程
      - 更新线程同时更新缓存A和缓存B。



##### 如何解决redis的并发竞争key问题

> 有多个子系统去set一个key。这个时候要注意什么呢？大家思考过么。
>
> 不推荐使用redis的事务机制。因为生产环境，基本都是redis集群环境，做了数据分片操作。你一个事务中有涉及到多个key操作的时候，这多个key不一定都存储在同一个redis-server上。

- 对这个key操作，不要求顺序

  - 准备一个分布式锁，大家去抢锁，抢到锁就做set操作(赋值)即可，比较简单

- 如果对这个key操作，要求顺序
  - 假设有一个key1,A将key1设置为valueA,B将key1设置为valueB,C将key1设置为valueC.期望按照key1的value值按照 valueA-->valueB-->valueC的顺序变化。这种时候我们在数据写入数据库的时候，需要保存一个时间戳。假设时间戳如下
    - 系统A key 1 {valueA  3:00}
    - 系统B key 1 {valueB  3:05}
    - 系统C key 1 {valueC  3:10}
    - 假设这会系统B先抢到锁，将key1设置为{valueB 3:05}。接下来系统A抢到锁，发现自己的valueA的时间戳早于缓存中的时间戳，那就不做set操作了。以此类推。

- 利用队列，将set方法变成串行访问



##### redis分布式锁

> 分布式系统中，两个不同的 JVM 里面，他们加的锁只对属于自己 JVM 里面的线程有效，Java 提供的原生锁机制在多机部署场景下失效

- 加锁: 实际上就是在redis中，给Key键设置一个值，为避免死锁，并给定一个过期时间

  > SET lock_key random_value NX PX 5000

    - random_value 是客户端生成的唯一的字符串
    - NX 代表只在键不存在时，才对键进行设置操作
    - PX 5000 设置键的过期时间为5000毫秒

- 解锁: 将Key键删除

  - LUA脚本完成，判断当前锁的字符串是否与传入的值相等，是的话就删除Key，解锁成功

- 三个要素: 加锁、解锁、锁超时
- 框架: Redisson



##### redis持久化

> Redis 是内存型数据库，为了保证数据在断电后不会丢失，需要将内存中的数据持久化到硬盘上

- RDB 持久化: 快照复制到其它服务器从而创建具有相同数据的服务器副本
- AOF 持久化: 设置同步选项，从而确保写命令同步到磁盘文件上。这是因为对文件进行写入并不会马上将内容同步到磁盘上，而是先存储到缓冲区，然后由操作系统决定什么时候同步到磁盘。



##### redis中支持事务吗？
支持，使用**multi开启事务**，使用**exec提交事务**。



##### redis发布订阅

- Redis 客户端可以订阅任意数量的频道
- Client们订阅了Channel时，当Channel有数据则发布给Client



##### 为什么redis使用跳表而不是用红黑树

- 跳表操作时间复杂度和红黑树相同
- 跳表代码实现更易读
- 跳表区间查找效率更高



##### 应用场景

当有用户为一篇文章点赞时，除了要对该文章的 **votes 字段进行加 1 操作**，还必须**记录该用户已经对该文章进行了点赞**，防止用户点赞次数超过 1。可以建立文章的已投票用户集合来进行记录。

![title](/images/2021年春招准备-redis-prometheus-rocketmq篇/1.png)



#### 2、Prometheus

##### 架构

![title](/images/2021年春招准备-redis-prometheus-rocketmq篇/2.png)

- Prometheus Server：用于抓取和存储时间序列化数据
- Exporters：主动拉取数据的插件
- Pushgateway：被动拉取数据的插件
- Altermanager：告警发送模块
- Prometheus web UI：界面化，也包含结合Grafana进行数据展示或告警发送
- 默认端口号为: 9090

##### Prometheus如何存储数据

- 采用time-series(时间序列)方式，存储在本地硬盘

- time-series数据库以**每2小时间**隔来分block(块)存储，**每个块又分为多个chunk文件**，chunk文件用来存放采集的数据的数据，metadata和索引文件；

- index文件是对metrics和labels进行索引之后存储在chunk中，**chunk是作为基本存储单位**，index和metadata作为子集；

- prometheus平时采集到的数据先存放在内存之中，对内存消耗大，以缓存的方式可以加快搜索和访问

- 在prometheus宕机时，prometheus有一种保护机制WAL，**将数据定期存入硬盘中以chunk来表示**，在重新启动时，可以恢复进内存当中。

- 当通过API删除序列时，**删除的记录存储在单独的tombstone文件中**(而不是立即从块文件中删除数据)



##### 数据采集的方式

- pull

  - 被监控主机安装各类已有的exporters
  - exporters以守护进程的模式运行，并开始采集数据
  - exporters是http_server，可以对http请求作出响应，并返回K/V数据，也就是metrics
  - prometheus通过用pull的方式（HTTP_GET）去访问每个节点上的exporter并采集回需要的数据

- push

  - 被监控主机安装官方的pushgateway插件
  - 通过自行编写的各种脚本，将监控数据组织成K/V的形式（metrics形式）发送给pushgateway
  - pushgateway再推送给prometheus
  - pushgateway只是一个中间转发的媒介，可以被安装在任何地方

##### 什么是metrics

  - metrics是对采集过来的数据的一种统称
  - 类型:
      - Gauges: 最简单的度量指标，只有一个简单的返回值，或者叫做瞬时状态
      - Counters: Counters就是计数器
      - Histograms：柱状图，用于观察结果采样
      - Summary：类似Histogram，用于表示一段时间内数据采样结果

##### 什么是node-exporter

> Prometheus为了支持各种中间件以及第三方的监控提供了exporter，监控适配器，将不同指标类型和格式的数据统一转化为Prometheus能够识别的指标类型。他们将这些异构的数据转化为标准的Prometheus格式，并提供HTTP查询接口。 
>
> **默认监听9100端口** 

- Node exporter主要通过读取Linux的/proc以及/sys目录下的系统文件获取操作系统运行状态
- redis exporter通过Redis命令行获取指标
- mysql exporter通过读取数据库监控表获取MySQL的性能数据

##### PromQL

> PromQL是Prometheus内置的数据查询语言，其提供对时间序列数据丰富的查询，聚合以及逻辑运算能力的支持

**matric固定格式**: `<metric name>{<label name>=<label value>, ...}`

```shell
node_memory_MemFree_bytes{instance="node02",job="node02"}
```

**监控名称查询**: 

```shell
# 如下查询了cpu的空闲和非空闲时的使用时间
node_cpu_seconds_total{mode='idle'} 
node_cpu_seconds_total{mode!='idle'} 
node_systemd_unit_state{instance='47.98.138.176:9100', job='node', name='docker.service'}
```

**范围查询:** 

- `s` - 秒
- `m` - 分钟
- `h` - 小时
- `d` - 天
- `w` - 周
- `y` - 年

```shell
pushgateway_http_requests_total{instance=~"pushgateway",method='get'}[1m]
```

**时间位移查询**

```shell
pushgateway_http_requests_total{instance=~"pushgateway",method='get'} offset 1d
```

**聚合查询**

````shell
# 查询昨天1天内pushgeteway中get的请求总量之和
sum(pushgateway_http_requests_total{instance=~"pushgateway",method='get'} offset 1d)

# 按照mode计算主机CPU的平均使用时间
avg(node_cpu_seconds_total)by(mode)
````



#### 3、RocketMQ

##### MQ选型

- 初步考虑
  - 业内常用的MQ有哪些？
  - 每一种MQ各自的表现如何？
  - 这些MQ在同等机器条件下，能抗多少QPS（每秒抗几千QPS还是几万QPS）？
    性能有多高（发送一条消息给他要2ms还是20ms）？
  - 可用性能不能得到保证（要是MQ部署的机器挂了怎么办）？
- 业界MQ
  - RabbitMQ: 
    - 当大量消息积压的时候，会导致 RabbitMQ 的性能急剧下降
    - 每秒钟可以处理几万到十几万条消息
    - 中小公司对并发和吞吐量要求不高的场景
    - 吞吐量低，集群扩展麻烦
  - RocketMQ: 
    - java开发
    - 吞吐量高，QPS十万级、性能毫秒级
    - 支持集群部署
    - 适当丢失数据没有关系、吞吐量要求高、不需要太多的高级功能的场景
  - Kafka: 
    - 每秒钟消息数量没有那么多的时候，Kafka 的时延反而会比较高。
    - 日志消息队列
    - 吞吐量所有MQ里最优秀，QPS十万级、性能毫秒级、支持集群部署
    - 丢数据， 因为数据先写入磁盘缓冲区，未直接落盘。
- 调研组件技术性能，开源社区活跃程度等。大型的软件公司，OLTP场景下都会倾向于使用RocketMQ

##### MQ作用

- 解耦：系统耦合度降低，没有强依赖关系
- 异步：不需要同步执行的远程调用可以有效提高响应时间
- 削峰：请求达到峰值后，后端service还可以保持固定消费速率消费，不会被压垮
  - **同样的机器配置下，如果数据库可以抗每秒** **6000** **请求，** **MQ** **至少可以抗每秒几万请求**

##### 架构

![title](/images/2021年春招准备-redis-prometheus-rocketmq篇/3.jpg)

![title](/images/2021年春招准备-redis-prometheus-rocketmq篇/4.jpg)

消息先发到Topic，然后消费者去Topic拿消息。只是Topic在这里只是个概念，实际上Message是在每个Broker上以Queue的形式记录。

- 消费者发送的Message会在Broker中的Queue队列中记录
- 一个Topic的数据可能会存在多个Broker中
-  一个Broker存在多个Queue
- 单个的Queue也可能存储多个Topic的消息
- 每个逻辑队列保存一部分消息数据，但是保存的消息数据实际上不是真正的消息数据，而是指向commit log的消息索引
- Queue不是真正存储Message的地方，真正存储Message的地方是在CommitLog

##### 部署模型

![title](/images/2021年春招准备-redis-prometheus-rocketmq篇/5.png)

- NameServer: NameServer相当于集群管理服务，主要用于管理整个集群的元数据以及对集群进行管理的
- Broker: 单个节点负责数据的读写的，Broker在部署的时候，一般是分为主备角色的
- Producer
- Consumer

**工作流程**:

- 启动NameServer，接着启动Broker
- Broker启动之后就会跟每一个NameServer建立长连接，每隔一段时间(30s)发送心跳包过去，心跳包里需要包含自己当前存储的数据信息，让NameServer感知到各个Broker的情况
- 创建 Topic，创建 Topic 的时候就会决定这个 Topic 的数据会放在哪些Broker上
- Broker在进行心跳的时候，上报自己当前存储的Topic相关的数据信息给NameServer
- NameServer就保存了整个集群的元数据
- Producer在生产消息到Topic的时候，先得找NameServer问一下Topic存放在哪些Broker上，然后就可以跟那些Broker建立连接发送消息过去了
- Consumer要从Topic消费消息，也会找NameServer问一下可以从哪些Broker上消费消息，接着同样会跟Broker建立连接并且开始消费，Consumer在多台机器上，默认也会把Topic下的多个队列的数据均匀的分配给各个Consumer实例来进行消费

##### RocketMQ Broker中的消息被消费后会立即删除吗

- 每条消息都会持久化到CommitLog中，每个Consumer连接到Broker后会维持消费进度信息，当有消息消费后只是**当前Consumer的消费进度（CommitLog的offset）更新**了

- 4.6版本默认48小时后会删除不再使用的CommitLog文件



##### RocketMQ消费模式有几种

- 集群消费
  - 一条消息只会被同Group中的一个Consumer消费
  - 多个Group同时消费一个Topic时，每个Group都会有一个Consumer消费到数据
- 广播消费
  - 消息将对一个Consumer Group 下的各个 Consumer 实例都消费一遍



##### 消费消息是push还是pull

pull，broker主动推送消息的话有可能push速度快，消费速度慢的情况，那么就会造成消息在consumer端堆积过多



##### RocketMQ如何做负载均衡

> 通过Topic在多Broker中分布式存储实现

- producer
  - 发送端指定message queue发送消息到相应的broker
  - 默认策略
    - producer维护一个index
    - 每次取节点会自增
    - index向所有broker个数取余
    - 自带容错策略
- consumer
  - 采用的是平均分配算法来进行负载均衡
  - 当消费负载均衡consumer和queue不对等的时候会发生什么？
    - Consumer和queue会优先平均分配
    - 如果Consumer少于queue的个数，则会存在部分Consumer消费多个queue的情况
    - 如果Consumer等于queue的个数，那就是一个Consumer消费一个queue
    - 如果Consumer个数大于queue的个数，那么会有部分Consumer空余出来



##### 消息重复消费

> 影响消息正常发送和消费的**重要原因是网络的不确定性**

**引起重复消费的原因**

- ACK
  - 正常情况下在consumer真正消费完消息后应该发送ack，通知broker该消息已正常消费，从queue中剔除
  - 当ack因为网络原因无法发送到broker，broker会认为词条消息没有被消费，此后会开启消息重投机制把消息再次投递到consumer
- 消费模式
  - 在集群消费模式下，消息在broker中会保证相同group的consumer消费一次，但是针对不同group的consumer会推送多次

**解决方案**

- 数据库表: 处理消息前，使用消息主键在表中带有约束的字段中insert

- Redis: 分布式锁
- Map: 单机时可以使用map做限制，消费时查询当前消息id是不是已经存在



##### 如何让RocketMQ保证消息的顺序消费

- 多个queue只能保证单个queue里的顺序，多个queue同时消费是无法绝对保证消息的有序性的

- 同一topic，同一个QUEUE，发消息的时候一个线程去发送消息，消费的时候一个线程去消费一个queue里的消息

- **保证生产者 - MQServer - 消费者是一对一对一的关系**

  - 上一条发送完了，下一条发送
  - 世界上解决一个计算机问题最简单的方法：“恰好”不需要解决它！

  

##### RocketMQ如何保证消息不丢失

- Producer: 采取send()同步发消息，**发送结果是同步感知的**，发送失败后可以重试，设置重试次数
- Broker: 修改刷盘策略为同步刷盘,默认情况下是异步刷盘的;主从模式
- Consumer: 完全消费正常后在进行手动ack确认



##### RocketMQ如何保证消息只被消费一次

重复消费不可怕，可怕的是你没考虑到重复消费之后，怎么保证幂等性。

举个例子吧。假设你有个系统，**消费一条消息就往数据库里插入一条数据**，要是你一个消息重复两次，你不就插入了两条，这数据不就错了？但是你要是消费到第二次的时候，自己判断一下是否已经消费过了，若是就直接扔了，这样不就保留了一条数据，从而保证了数据的正确性。

**一条数据重复出现两次，数据库里就只有一条数据，这就保证了系统的幂等性**

幂等性: 一个数据，或者一个请求，给你重复来多次，你得确保对应的数据是不会改变的，不能出错

**解决**

比如拿个数据要写库

- 先根据主键查一下，如果这数据都有了，你就别插入了，update 一下好吧。

- 写 Redis，那没问题了，反正每次都是 set，天然幂等性



##### rocketMQ的消息堆积如何处理

- 先定位问题：要找到是什么原因导致的消息堆积，是Producer太多了，Consumer太少了导致的还是说其他情况
- 看下消息消费速度是否正常，正常的话，可以通过上线更多consumer临时解决消息堆积问题
- RocketMQ中的消息只会在commitLog被删除的时候才会消失，不会超时。也就是说未被消费的消息不会存在超时删除这情况
- 堆积的消息会不会进死信队列
  - 不会，消息在消费失败后会进入重试队列，重试N(16、18)次，才会进入死信队列



##### 如果让你来动手实现一个分布式消息中间件，整体架构你会如何设计实现

- 需要考虑能快速扩容、天然支持集群
- 持久化的姿势
- 高可用性
- 数据0丢失的考虑
- 服务端部署简单、client端使用简单



##### 消息去重

- 消费端处理消息的业务逻辑保持幂等性
- 保证每条消息都有唯一编号且保证消息处理成功与去重表的日志同时出现