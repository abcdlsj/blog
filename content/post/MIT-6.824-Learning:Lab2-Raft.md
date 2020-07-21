---
date: "2020-07-02T15:42:07+08:00"
title: "MIT 6.824 Learning: Lab2 Raft"
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

>  6.824 Lab 2: Raft

<!--more-->

> 不知道哪天我才能把 Lab2 磨蹭完。。。
>
> -\- 07-02 :timer_clock: 15:44
>
> 开始开始，什么时候完成就不知道了（可能今天写几个小时，明天就不写了，无所谓了）
>
> -\- 07-16 :timer_clock: 15:15

 ## A

### Task

实现 Raft 领导者选举和心跳（无 log 项的 `AppendEntries` RPCs）。Lab 2A 的目标是选举单 leader，leader 会保持身份直到发生错误，新的 Leader 会接管数据包（发送给/来自 old leader），`go test -run 2A` 测试 2A。

## Hint

### 实现

首先定义各种状态

## B

## C

## References

https://blog.csdn.net/weixin_43705457/article/details/105981661

https://zhuanlan.zhihu.com/p/103849249

