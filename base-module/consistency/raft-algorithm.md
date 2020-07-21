---
description: Raft 算法能够解决分布式环境下的一致性问题
---

# Raft 算法

## Raft 算法概念

Raft 算法是由斯坦福大学提出的一种分布式协议，相比 Paxos 而言更加的容易理解。Raft 算法是从多副本状态机的角度出发，它用来用于管理多副本状态机的日志复制从而实现了和 Paxos 同样的功能。

之所以 Raft 算法相对 Paxos 算法更加容易理解，原因是 Raft 算法将复杂的分布式一致性问题拆解为几个子问题：

* Leader 选举（Leader election）
* 日志同步（Log replicaiton）
* 安全性（Safety）
* 日志压缩（Log compaction）
* 成员变更（Membership change）

此外，相比 Paxos 直接从一致性协议推导，Raft 算法则使用了更强的假设来减少需要考虑的状态，从而使其变得更加容易理解和实现。

{% hint style="warning" %}
实际上，如果你不理解 Paxos 算法也不需要气馁，因为强如斯坦福大学和加州大学伯克利分校的高年级本科生和研究生在学习 Paxos 算法时也觉得难以理解；因此，学习这类复杂的东东需要一步一个脚印，不可能一下子就吃透！
{% endhint %}

## Raft 算法中的角色

Raft 算法将系统中的角色分为几个部分：

* **Leader**：接收客户端请求并向 Follower 同步请求日志，当日志到大大多数节点后告诉 Follower 提交日志
* **Follower**：接受并持久化 Leader 同步的日志，在 Leader 告知日志可以提交后提交日志
* **Candiadte**：Leader 选举过程中的临时角色

![raft-role](../../.gitbook/assets/raft-role.jpg)

## Raft 选举流程

Raft 要求系统在任意时刻最多只能有一个 Leader，正常工作期间只能有 Leader、Followers 这两个角色，关于 Raft 算法系统中的角色的状态描述即流程图如下：

* 开始，所有服务启动都是 Follower，在 Follower 超时没有收到 Leader 心跳之后，Follower 超时（没有收到任何来自 Leader 的心跳），此时它会成为一个 Candidate 并开始一次 Leader 选举
* 在 election 期间各个 Candidate 开始投票 election Leader
  * 如果在 Candidate 期间发现一个 Leader 或开启了一个新的 term，它就会从 Candidate 变为 Follower
* 此时收到大多数服务器投票的 Candidate 会成为新的 Leader
  * 如果经由投票产生的 Leader 发现在更早的 term 已经产生了一个 Leader 则此时它会转变为 Follower

![raft-role-change](../../.gitbook/assets/raft-election.jpg)

> 如果你需要动态的查看选举这样的流程，可以参考文末的链接 [The Raft Consensus Algorithm](https://raft.github.io/)，它提供了一个选举的动画可以帮助你理解、消化 Raft 的选举流程。

![](../../.gitbook/assets/raft-election-gif.jpg)

{% hint style="danger" %}
此外，需要特别指出的几点：
{% endhint %}

* Raft 是通过心跳（heartbeat）来触发选举流程的；Leader 周期性的向 Follower 发送心跳从而维持其 Leader 地位
* Follower 不会主动提出请求，只是响应 Leader 和 Candidate 的请求
* Leader 负责处理所有客户端请求（假如某个客户端先连接到 Follower，那么 Follower 要负责把这个客户端重定向到 Leader）

## 日志同步

> Raft 的日志由日志有序编号（log index）组成，每个日志条目（log entries）包含了它被创建时的任期号（term）

在选举出 Leader 之后，Leader 就可以开始接收客户端的请求。Leader 把请求作为日志条目（Log entries）加入到它的日志中，然后并行的向其他服务器发起 AppendEntries RPC 复制这些日志条目。当这些日志被复制到大多数服务器上，Leader 将这条日志应用到它的状态机并向客户端返回执行结果！

\*\*\*\*🌠 **如果某些 Follower 可能没有成功的复制日志（比如 Follower 宕机了），Leader 会无限的重试直到所有 Follower 最终存储了所有日志条目**

![raft-log](../../.gitbook/assets/raft-log.jpg)



## 安全性



## 日志压缩



## 成员变更



## Reference

* [The Raft Consensus Algorithm](https://raft.github.io/)
* [Raft Paper](https://raft.github.io/raft.pdf)
* [Raft 算法详解](https://zhuanlan.zhihu.com/p/32052223)

