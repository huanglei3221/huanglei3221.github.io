

# 关于Shared Nothing （Sharding）



shared nothing 架构

## 三种数据库构架设计架构：

主要有 Shared Everthting、Shared Nothing、和 Shared Disk：

1. Shared Everthting: 一般是针对单个主机，完全透明共享 CPU/MEMORY/IO，并行处理能力是最差的，典型的代表 SQLServer
2. Shared Disk：各个处理单元使用自己的私有 CPU 和 Memory，共享磁盘系统。典型的代表 Oracle Rac， 它是数据共享，可通过增加节点来提高并行处理的能力，扩展能力较好。其类似于 SMP（对称多处理）模式，但是当存储器接口达到饱和的时候，增加节点并不能获得更高的性能 。
3. Shared Nothing：各个处理单元都有自己私有的 CPU / 内存 / 硬盘等，不存在共享资源，类似于 MPP（大规模并行处理）模式，**各处理单元之间通过协议通信**，并行处理和扩展能力更好。典型代表 DB2 DPF 和 Hadoop ，各节点相互独立，**各自处理自己的数据，处理后的结果可能向上层汇总或在节点间流转**。
4. **我们常说的 Sharding 其实就是 Share Nothing 架构**，它是把某个**表从物理存储上被水平分割，并分配给多台服务器（或多个实例）**，每台服务器可以独立工作，具备共同的 schema，比如 **MySQL Proxy** 和 Google 的各种架构，只需增加服务器数就可以增加处理能力和容量。
5. Shared nothing 架构（shared nothing architecture）是一 种分布式计算架构。这种架构中的每一个节点（ node）都是独立、自给的，而且整个系统中没有单点竞争。
6. 在一个纯 Shared Nothing 系统中，通过简单地增加一些廉价的计算机做为系统的节点即可以获取几乎无限的扩展。
7. Shared nothing 系统通常需要将他的数据分布在多个节点的不同数据库中（不同的计算机处理不同的用户和查询）或者要求每个节点通过使用某些协调协议来保留它自己的应用程序数据备份 ，这通常被成为数据库 Sharding。





![image-20230422134451890](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422134451890.png)





## Shared Disk架构

![image-20230422202453609](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422202453609.png)







## Shared Nothing 所基于的理念：

参见：

https://www.youtube.com/watch?v=rlfdS34Y9fw

![image-20230422142005223](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422142005223.png)



举例：TeraData

![image-20230422202347833](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422202347833.png)

各自算各自的， 算完再开会





## 关于CAP原理

consistency （一致性） **, availability（可用性： 系统一直能提供服务）, and partition tolerance**（分区容错，分区之间如果出现数据差错还能否继续进行）.

分区容错意味着系统继续运行   分区容忍度的最简单示例是，即使参与提供服务的机器由于网络链接中断而失去相互通信的能力，系统仍继续运行

你会发现： 尤其是C和P是相矛盾的， 如果要高一致性， 那么对分区容错的容忍度就很小。



现在考虑两台服务器以主从关系协作的情况。两者都维护状态的完整副本，如果主服务器发生故障，从服务器将接管主服务器的角色，这是由心跳丢失决定的——也就是说，通常通过专用网络在两个服务器之间进行定期健康检查。如果两者之间的心跳网络被分区，slave会把自己提升为master，不知道原来的master已经up但是无法在心跳网络上通信。此时有两个主人，系统中断。这种情况称为**脑裂。**



SQL 和其他关系数据库使用术语**ACID**来描述它们在 CAP 三角形中的一侧。ACID 代表原子性（事务是“全有或全无”）、一致性（在每个事务之后数据库处于有效状态）、隔离性（并发事务给出与串行执行相同的结果）和持久性（已提交的事务的发生崩溃或其他问题时数据不会丢失）。提供较弱一致性模型的数据库通常将自己称为 NoSQL



不存在CA分布式数据库

完美的一致性 并不十分重要

mongodb  —— CP数据库， 是一个单主系统 ， 所以他保持一致性 ， 牺牲了高可用

Cassandra —— AP数据库。 宽列数据库， 无主架构

postgresql —— 单点的postgresql 是一个CP数据库





# MySQL  Proxy





# MariaDB Proxy

参见：

https://www.youtube.com/watch?v=d9_gvEKy1MA

基本上是这么个架构。

![image-20230422231225214](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422231225214.png)



可以直观的看到部署图：

![image-20230422230846250](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422230846250.png)



![image-20230422231334011](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422231334011.png)



### 读写分离

主备模式：

![image-20230422231625897](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422231625897.png)

主备模式下， 读是到从节点， 写是写到主节点去。



### 通过proxy来做读写分离，对上层屏蔽

![image-20230422231727922](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422231727922.png)



![image-20230422232107852](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422232107852.png)







# Postgresql Proxy



postgresql的shared nothing 分区

## 原理

代理节点和节点支持无缝扩展

![image-20230422214123528](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422214123528.png)



![image-20230422214418159](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422214418159.png)

PlProxy做了一层抽象， 查询数据的时候， 调用PLProxy 写的函数

解析为提交给数据节点的SQL， 解析出来的SQL就交给数据节点执行，然后再返回给APP

## 几种运行模式

 旁路模式： 在PLProxy 中进行汇总，而不把函数下发给节点执行 

CLUSTER模式1： 配置存在SQL/MED配置的集群信息， 通过libpq_async API发送解析的SQL给数据节点（多个则并行）  等待所有数据节点返回结果给应用程序。

CLUSTER模式2： 和CLUSTER1的区别在于： 配置信息存在专门配置表。

![image-20230422215146207](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422215146207.png)

## 关于连接池

1） plproxy 到数据节点为长连接， plproxy和数据节点之间一般可以不用连接池

![image-20230422215402242](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422215402242.png)



2） 应用和proxy之间建议搞连接池



![image-20230422225105717](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422225105717.png)



参见： 

https://www.bilibili.com/video/BV127411G7PX?p=17&vd_source=1115a1b57e46edddf88be0738ef3f5b2



## postgresql怎么做分片运算

![image-20230423003352537](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423003352537.png)

![image-20230423003335510](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423003335510.png)

### 分片

#### Hash分片：

![image-20230423003628722](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423003628722.png)



![image-20230423003648091](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423003648091.png)



#### 范围分片

![image-20230423003942140](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423003942140.png)

![image-20230423004005317](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423004005317.png)



### Join的计算方式： 

一种是  "broadcast join"

当一张表比较小， 就可以广播

![image-20230423010142685](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423010142685.png)

一种是 merge join 

![image-20230423004829848](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423004829848.png)

还有一种分布式join， 适用于

![image-20230423005151045](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423005151045.png)

### 关于索引

![image-20230423010330435](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423010330435.png)

是不是可以有反向索引？

![image-20230423010542692](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423010542692.png)









### 哪些SQL语法在分布式数据库上运行效率高：

![image-20230423005448126](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423005448126.png)



![image-20230423005726617](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423005726617.png)



# Zookeeper



zookeepr保证最终一致性，尽量保证强一致性

![image-20230422235705893](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422235705893.png)



怎么选出leader？ 

![image-20230422235959981](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422235959981.png)



关键问题： 这个PK怎么实现



![image-20230423000748414](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423000748414.png)

## zookeeper的脑裂问题

大集群， 因为网络断掉了出现了两个小集群，出现了脑裂。![image-20230423001309062](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230423001309062.png)

出现脑裂问题非常严重。

如果是图上这样， 因为右侧只有两个节点， 选不出leader，所以右侧集群失效。

zookeeper通过过半机制解决脑裂问题： 要么有leader，要么没有leader，不会出现两个leader

为什么尽量用 奇数节点， 不要偶数节点？ 防止出现没有leader的情况









参见一下： 

https://www.youtube.com/watch?v=sRZOvIEX4xo



# Postgresql原理



### Join

![image-20230422233835845](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422233835845.png)

#### nestloop

nestloop就是全表扫描， 笛卡尔积了。

一般当join条件没有=关联的时候 容易出现。

#### HashJoin

什么时候适合hashjoin：

参见：

https://www.cybertec-postgresql.com/en/join-strategies-and-performance-in-postgresql/

可见：当表都不小，并且较小的表的hash表正好能装在work_mem中， 这样才能发挥作用，否则，hash桶就要放在磁盘了。

![image-20230422233256742](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422233256742.png)



#### mergejoin：

可见使用场景是 内外部表都非常大， 并且hash桶都装不到workmem

如果有索引， mergejoin会非常快

![image-20230422234020025](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422234020025.png)





# Greenplum



![image-20230422114825244](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230422114825244.png)

