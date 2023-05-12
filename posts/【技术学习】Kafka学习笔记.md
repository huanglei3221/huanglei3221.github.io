---
layout: post
title:  "【技术学习】Kafka学习笔记"
date:   2023-05-11 10:07:24 +0800
tags: thinking
categories: 技术
---

# Kafka学习笔记



参见视频：

https://www.bilibili.com/video/BV1vr4y1677k/



## 使用Kafka的场景

双11的这种场景下， 数据产生的速度大于 数据处理的速度， 所以需要先用kafka缓存下来。

![Image](assets/images/Kafka学习笔记/Image.png)





## KAFKA的作用

1） 解耦： 不需要数据源和目的地之间建立联系， 只需要到kafka里面存取

![Image](assets/images/Kafka学习笔记/Image-16838070160022.png)

2） 异步通信：先存下来， 再等到需要的时候再处理





![ScreenClip](assets/images/Kafka学习笔记/ScreenClip.png)





## Kafka消息队列的两种模式：

![ScreenClip](assets/images/Kafka学习笔记/ScreenClip-16838070952245.png)



一种是点对点模式，消费完就删除

一种是发布订阅模式，消费完不删除，其他消费者还可以继续消费



## KAFKA的基础架构

### 1） 分区（Partition）： 

一个TOPIC被分成了多个partition， 一个partition在一个机器（Broker）上

### 2） 消费者组（ConsumerGroup）： 

一个消费者组里面有多个消费者， 但是在消费者组中， 一个partition 只能被一个消费者消费

![ScreenClip](assets/images/Kafka学习笔记/ScreenClip-16838074399859.png)

GPT Tips：



| GPT Tips：                                                   |
| ------------------------------------------------------------ |
| 在 Kafka 中，一个 partition 只能被一个 consumer group 中的一个 consumer 实例消费。这意味着同一个 consumer group 中的多个 consumer 实例可以分摊消费多个 partition，但一个 partition 只能由一个 consumer 实例消费。这种分配方式确保了在一个 consumer group 内的消费者实例之间的负载均衡，并确保每个消息只会被 consumer group 中的一个消费者实例处理。这样可以保证消息的顺序性和一致性。如果多个 consumer group 中的消费者实例订阅了相同的 topic，它们可以独立消费各自分配到的 partition。这样可以实现并行消费，提高整体的消费能力。需要注意的是，Kafka 中的 partition 是消息的物理分组单位，每个 partition 在任意时刻只能由一个 consumer 实例消费，但一个 consumer 实例可以同时消费多个 partition。 |

### 3） 有主备机制

 kafka集群中的partition有leader 和 follower

![ScreenClip](assets/images/Kafka学习笔记/ScreenClip-16838072788657.png)

### 4） 部分数据存储在zookeeper

![Image](assets/images/Kafka学习笔记/Image-168380746272011.png)

kafka2.8之后， 可以不用zookeeper ， zookeeper 已经成为kafka瓶颈



### 5） 整体功能分三块：

三块： producer （java）consumer （java）broker （scala）



## Kafka  Producer

### 基本架构

![Image](assets/images/Kafka学习笔记/Image-168380758105717-168380923767325.png)

拦截器可用可不用（一般不用）， 序列化器进行序列化，如果是文本基本也用不到。分区器中有个部分叫 RecordAccumulator ， 它是一个缓存（32M）每个批次是16k 这是一个双端队列。 这里也用到了内存池。 数据被缓存到这里面， 按批次进行缓存。发送线程看到数据积累到batchsize之后， 才会发送数据。或者达到linger_ms也会发送



**GPT TIPS：**

*在 Kafka 中，Sender 是负责将消息发送到 Kafka 集群的组件之一，而 RecordAccumulator 则是 Sender 内部的一个重要组件。*
*Sender 的主要作用是将消息从生产者发送到 Kafka 集群的 Broker。当生产者发送消息时，Sender 负责将消息添加到 RecordAccumulator 中进行缓冲和批量发送。*
*RecordAccumulator 是一个用于消息缓冲和批量发送的缓冲区。它接收来自生产者的消息，并按照指定的分区策略将消息累积到不同的分区中。同时，RecordAccumulator 会根据指定的时间、大小等条件进行消息的批量聚合，以减少网络开销和提高效率。*
*具体的工作流程如下：*

	1. *生产者发送消息：生产者将消息发送给 Sender。*
	2. *Sender 添加消息到 RecordAccumulator：Sender 将消息添加到 RecordAccumulator 中，根据消息的分区策略将消息放入对应的分区中。*
	3. *消息批量聚合：RecordAccumulator 在一段时间内（例如，一定时间间隔或达到一定消息数量）对消息进行批量聚合，以减少发送的网络开销。*
	4. *批量发送消息：当消息满足批量发送的条件时，RecordAccumulator 将批量消息发送给 Kafka 集群的 Broker。*
	5. *确认消息发送：一旦消息成功发送到 Broker，并收到 Broker 的确认响应（acknowledgment），Sender 将通知生产者消息发送成功。*

*通过使用 RecordAccumulator，Kafka 实现了高效的消息缓冲和批量发送机制。这样可以提高消息发送的吞吐量和效率，减少网络开销，并且允许更大的批量处理。*
*需要注意的是，RecordAccumulator 的配置参数可以根据具体的需求进行调整，以平衡延迟和吞吐量之间的权衡。*



发送的时候， 一个分区的数据以broker为key， 放到一个队列中，如图：

![Image](assets/images/Kafka学习笔记/Image-168380920673019-168380921492923.png)



#### 关于sender线程的个数：

在 Kafka 的 Producer 中，发送消息的工作由一个或多个 Sender 线程执行。具体有多少个 Sender 线程取决于配置参数和生产者的使用情况。

在 Kafka 0.8.x 版本中，每个生产者实例通常会有一个单独的 Sender 线程来处理消息的发送。这个 Sender 线程负责将消息从生产者的缓冲区发送到 Kafka 的 Broker。

从 Kafka 0.9 版本开始，引入了异步发送（Asynchronous Send）的机制，允许多个 Sender 线程并行地发送消息。这样可以提高生产者的并发性能和吞吐量。发送线程的数量可以通过配置参数 `max.in.flight.requests.per.connection` 来控制，默认值为 5。

需要注意的是，Sender 线程并不是一个固定的数量，而是根据配置和负载情况动态调整的。当生产者需要发送消息时，它会根据配置的参数和当前的负载情况动态创建或销毁 Sender 线程，以适应不同的生产者工作负载。

因此，具体有多少个 Sender 线程取决于 Kafka 版本、生产者的配置参数和使用情况。在生产者的工作中，可以通过适当的配置来控制 Sender 线程的数量，以满足性能和吞吐量的需求。



#### 应答机制

kafka集群收到之后有一个应答机制， 级别为0,1，-1

1）如果应答级别为0 ， 则不管成功与否继续发。

2） ack=1 leader 收到就继续发

3） ack=-1 或者 all 就意味着leader 和 follower 都收到之后才应答生产者客户端收到应答之后， 清理缓存和队列。 如果没有收到应答， 则继续发送。直到一定量（5个）的请求都没有收到应答为止, 如果一定时间还没有收到应答， 则retry



#### 三种发送方式：

1） 异步发送， 居多

2） 异步回调发送

有个callback

![image-20230511210243689](assets/images/Kafka学习笔记/image-20230511210243689.png)

3） 同步发送



#### 分区

##### 分区的好处：

1） 便于合理使用存储资源， 实现负载均衡效果

例如： 磁盘1T, 100T 

2） 提高并行度， 生产者可以以分区为单位发送数据， 消费者可以以分区为单位消费数据



##### 分区方式

1） 指定分区

2） 没有partition但又key， 则hash再取余分区

![image-20230512100042195](assets/images/Kafka学习笔记/image-20230512100042195.png)

3） 粘性分区

![image-20230512100131464](assets/images/Kafka学习笔记/image-20230512100131464.png)





#### 提高吞吐量的方法

1） 增加batch.size  批次大小， 默认16k

​        linger.ms 等待时间

2） compression.type 压缩snappy

3） RecordAccumulator 缓冲区大小 ， 修改为64M等



#### 数据可靠性

##### 1） ACK=0， 很少使用

![image-20230512100428708](assets/images/Kafka学习笔记/image-20230512100428708.png)

##### 2）ACK=1 ，  leader落盘就能应答

这种情况下， 有小可能会丢数据

![image-20230512100451265](assets/images/Kafka学习笔记/image-20230512100451265.png)

##### 3） ACK=-1（all） 数据同步完再应答

![image-20230512100534508](assets/images/Kafka学习笔记/image-20230512100534508.png)

![image-20230512100603902](assets/images/Kafka学习笔记/image-20230512100603902.png)

Kafka  引入了ISR机制进行保护。



##### 数据完全可靠条件

![image-20230512100657650](assets/images/Kafka学习笔记/image-20230512100657650.png)

![image-20230512100749098](assets/images/Kafka学习笔记/image-20230512100749098.png)





#### 数据重复问题

##### 幂等性原理

![image-20230512100912955](assets/images/Kafka学习笔记/image-20230512100912955.png)

![image-20230512101001075](assets/images/Kafka学习笔记/image-20230512101001075.png)



##### 事务

![image-20230512101031110](assets/images/Kafka学习笔记/image-20230512101031110.png)





##### 数据有序





















