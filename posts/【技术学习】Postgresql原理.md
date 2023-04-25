---
layout: post
title:  "【技术学习】Postgresql原理学习"
date:   2023-04-25 10:07:24 +0800
tags: thinking
categories: 技术
---


# Postgresql原理学习

Postgresql原理学习建议看彭志勇的书以及Postgresql的官方文档：

现在Postgresql已经出到了14.7版本， 就以14.7版本作为蓝本学习吧。





## 内存管理





内存管理，参见： 

![image-20230328153926249](D:/codes/huanglei3221.github.io/posts/assets/images/postgresql内核和UDA/image-20230328153926249.png)

![image-20230328154001171](D:/codes/huanglei3221.github.io/posts/assets/images/postgresql内核和UDA/image-20230328154001171.png)



Context本身是一个trunk， trunk里面又分了block。有freelist来管理当前空闲的block

![image-20230328154108439](D:/codes/huanglei3221.github.io/posts/assets/images/postgresql内核和UDA/image-20230328154108439.png)



freelist里面又会记录每种大小的block分别分配了多少

![image-20230328154511194](D:/codes/huanglei3221.github.io/posts/assets/images/postgresql内核和UDA/image-20230328154511194.png)





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



