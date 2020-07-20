---
description: CAP：Consistency、Availability、Partition Tolerance
---

# Base 理论与 CAP 原则

## CAP 概念

{% hint style="info" %}
**CAP定理：在一个分布式系统中，CAP 原则这三个要素最多只能满足其中两个要素，无法三者均满足！**
{% endhint %}

CAP 原则也叫 CAP 定理，他描述的是在一个分布式系统中 CAP 的一个关系，所谓 CAP，即 Consistency、Availability、Partitiontolerance。

* 一致性（**Consistency**）：分布式系统中的所有数据备份在同一时刻是否都是同样的值
* 可用性（**Availability**）：在分布式系统中，集群中的部分节点故障后，真个集群是否还能正常的响应客户端的请求
* 分区容错性（**Partition Tolerance**）：在分布式系统中，如果在同一时刻无法满足数据一致性，就意味着发生了分区，这时候就必须在 C 与 A 见做出选择

![cap](../../.gitbook/assets/cap.png)

如上图所示，分布式系统只能是 AP、CP、CA 这三种情况！

## BASE 理论

BASE 理论是 **Basically Available**（基本可用）、**Soft state**（软状态）和 **Eventually consistent**（最终一致性）三个短语的简写。

Base 理论是对 CAP 中的一致性和可用性权衡的结果，简单的说：即使分布式系统无法做到强一致性，那么也应该根据自身系统特点从而使系统达到最终一致性。

* 基本可用：指允许系统在出现不可预知的故障错误时，允许损失部分可用性
* 软状态：指系统中的数据允许存在中间状态，这种状态的数据存在不会影响系统的整体可用性；换句话说允许系统在不同节点的数据副本之间进行数据同步的过程时允许存在延时
* 最终一致性：数据必须要有一个最终状态，不要求要时序性上保障数据的强一致性

## Reference

* [百度百科 CAP](https://baike.baidu.com/item/CAP%E5%8E%9F%E5%88%99/5712863?fr=aladdin)

