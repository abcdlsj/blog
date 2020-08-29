---
date: "2020-06-14T17:42:58+08:00"
title: "MIT 6.824 Learning: Raft Paper Reading Notes (WIP)"
authors: []
categories:
  - learn
tags:
  - 6.824
  - Distribution
  - Raft
draft: true
toc: true
typora-root-url: ../../static
---

> Raft 论文阅读笔记

<!--more-->

> <!--开始看了。。。（hhhh，拖延了半个月）-->
>
> <!---\- 06-30-->
>
> <!--今天看了差不多 3 小时，就只看了论文，还很多没看。。。-->
>
> <!---\- 07-02-->
>
> <!--过了好多天了，又回来继续写 lab 了，学习不能停，一停就停不住了。。-->
>
> <!---\- 07-16-->

## Consensus

分布式系统最重要的抽象之一就是共识（consensus）：就是让所有的节点对某件事达成一致。

> raft 论文为了实现共识，采用了：
>
> 1. Leader Election
>
> 2. Log Replication

### Leader Election

每个节点都只会有三种状态：

- Leader
- Follower
- Candidate

一般，集群中包含一个 Leader 和参与者，Leader 负责处理所有的客户端请求，如果客户端请求连接到了参与者，会被重定向到 Leader。

![Servers states](/img/image-20200702144619898.png)

Followers 只响应其它 Servers 的请求，如果一个 Follower 没有接受到任何消息，它就会成为一个 Candidate（候选人）并且发起选举，如果收到整个集群大多数 Server 的投票，那么它就会成为一个 Leader。

![Servers terms](/img/image-20200702144503274.png)

> 分布式的本质是时间问题

不同的 Server 可能会在不同的时间观察到不同的转换，在某些特殊情况下，一个 Server 可能观察不到一次选举或者一整个任期。每个 Server 都用一个变量储存当前任期，并随着时间单调递增。当 Servers 进行通讯的时候，也会交换当前的任期。如果一个 Server 任期小于其它机器，就会更新自己的任期到最大任期。如果是一个候选者（Candidate） 或 Leader 发现自己任期过期，就会转变到 Follower。Server 如果收到过期的请求，就会拒绝该请求。

Raft 服务之间采用 RPC 进行通信，基础的 Raft 只需要两种类型的 RPC，RequestVote RPCs 由候选者在选举期间使用，AppendEntries RPCs  由 Leader 进行日志复制和发送心跳时使用。

**选举过程**

Servers 启动时都是作为参与者。参与者收到来自 Leader 或者候选者的**合法**请求，就会保持参与者状态。Leader 会发送心跳到其它的 Server 来授权延长任期，如果一个参与者的选举定时器超时的时候没有收到任何请求，就会发起新的选举。

一次选举开始时，参与者增加自己本地存储的当前任期然后转变为候选者状态。这个候选者先选举自己，并行的给集群中的其它机器发送 RequestVote RPCs。候选者将会一直保持候选状态直到下面三件事情中的任意一件发生:

1. **候选者本次选举胜出**

   > 一个候选者如果接受到集群中大多数机器在同一个任期的选票，那么它将胜出成为 Leader。一旦一个候选者胜出将成为集群的 Leader，它将会并行的给集群的其它机器发送心跳来宣示自己胜出，并阻止进行新的选举。

2. **一台机器确认自己是 Leader**

   >  等待选举的过程中，一个候选者可能接收到来自其它 Server 的请求，该请求申明该 Server 已经成为 Leader，如果请求中的任期大于本地任期，那么认为请求合法并转变为 Follower，如果小于，则拒绝请求并保持候选者状态。

3. **僵持一段时候没有人胜出**

   >  整个集群的候选者都没人胜出，所有候选人的选举定时器会超市，它们增加自己的任期并启动新一轮选举。

选举分裂：没有一个候选者获得半数以上的选票。

Raft 通过随机选举定时器来阻止选举分裂的发生，即使选举分裂发生也可以很快的被解决（[150, 300] 之间随机生成）。

### Log Replication

一旦一个候选者成为 Leader，它将开始处理客户端的请求。每个请求包含一个需要由复制状态机执行的命令，Leader 将该命令作为一个新的条目添加到日志中，然后通过 AppendEntries RPCs 并行发送到其它 Servers 来进行条目复制。

当日志安全复制之后，Leader 将日志应用到自己的状态机并将结果返回给客户端。如果集群中有参与者宕机、处理速度慢、丢包等，Leader 会重试 AppendEntries RPCs 请求直到日志被安全复制。

![Log entrys](/img/image-20200702153759613.png)

日志的格式如上：

![Log indexs](/img/image-20200702153519693.png)

![Figure 3](/img/Figure 3.png)

## Raft



## References

https://www.gitbook.com/book/vonng/ddia-cn 

https://thesquareplanet.com/blog/students-guide-to-raft/

http://thesecretlivesofdata.com/raft/ （可视化网站，很有帮助）

https://github.com/maemual/raft-zh_cn/blob/master/raft-zh_cn.md （raft 中文翻译，适合对照着看）

https://izualzhy.cn/notes-on-raft 

