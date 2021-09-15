---
layout: post
title: 分布式事务简单探讨
---

## distributed transaction

distributed transaction包含两部分内容：
1. concurrency control
2. distributed reliability protocols

distributed reliability protocols包含两个方面：
1. commit protocols
2. recovery protocols

最流行的commit protocols是2PC。

### distributed reliability protocols

虽然数据是distributed和replicated的，但这不能保证数据的reliability和availability。所以需要distributed reliability protocols去把分布式架构的资源利用好来提供可靠和可用性。

即使部分节点失败，也不会影响服务。

### transaction的特点

transaction的特性: ACID
1. Atomicity
2. Consistency
3. Isolation
4. Durability

这四个特性一起，提供了reliable and consistent computing。

transaction是reliable computing和consistent computing的代名词。所以后面探讨consistency和reliability。 

### transaction consistency

和database consistency有区别。
1. database consistency要求数据库里的数据满足一些规则
2. transaction consistency表示所有的replicated database都在同一个状态

database consistency，也就是integrity。属于semantic data control里的内容。

#### semantic data control

它包含：
1. view management
2. security control
3. semantic integrity control

semantic data control的本质是a set of rules。而这些rules相当于生产数据的meta data，它们被存储在另外一个数据库中。这个数据库也称做directory和catalog。无论是centralized或distributed DBMS，都有这个directory或catalog。在distributed DBMS中，就叫做global directory。这个global directory存储的是关于fragment的信息。

### transaction reliability

即使部分节点失败，也能恢复。


### 总而言之

transaction management就是要使数据库在并行访问和失败出现时仍然能够保持在一个consistent的状态。
