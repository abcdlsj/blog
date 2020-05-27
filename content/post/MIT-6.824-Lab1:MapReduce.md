---
title: "MIT-6.824 Lab 1: MapReduce"
date: 2020-05-20T23:34:41+08:00
lastmod: 2020-05-20T23:34:41+08:00
draft: false
keywords: ["MIT-6.824 Lab 1: MapReduce"]
description: ""
tags: ["MapReduce", "Distribution"]
categories: ["learn"]
author: "abcdlsj"
typora-root-url: ../../static
---
> 6.824 Lab 1: MapReduce
<!--more-->

> 4 月份快结束的时候，我发觉我可能找不到实习了，遂放弃找实习（然后网易来面试了，然后毫无疑问的挂了），我当时准备还是先看完 6.824（项目 ~~比较少~~ 没有，想写完 lab 后写到简历上），于是先看了点 Golang，然后就没然后了。。。貌似一旦决定不找实习，好像就没动力了。。。天天躺尸（玩手机），这几天感觉不能这样了，要不然别说秋招找工作了，明年春招也找不到，于是准备开始写 lab1，视频是看完的，论文也看得差不多了（才开始 == 差不多）（事实证明囫囵吞枣不可取）。
>
> -\- 2020-05-20
>
> 四天过去了。。。还没动，昨天用 Golang ~~写~~ 抄了个遗传算法（？我为啥去会看这个），今天一定把 lab1 起个头！！！（下午还要交作业，唉，太惨了）（好了，写了这么多字可以休息了。。。）
>
> -\- 2020-05-24
>
> 这两天断断续续看了好几个小时，发现难度好大（之前学校从来没有科研经历，菜鸡一个），发现.....啥都不会。于是继续看论文，做笔记（用博客强制自己做笔记），今年这个 lab，让人有点摸不着头脑，我感觉前两年的任务都比较明确（菜鸡我也是道听途说），今年就让学生自己发挥。。。继续看吧，才开始呢！
>
> -\- 2020-05-27

## 论文阅读笔记

>  **MapReduce 输入输出格式**

![mapreduceInOut](/img/kvValues.png)

> MapReduce 工作流程，序号对应图中的序号

![mapreduce](/img/mapreduce.png)

1. 用户调用 MapReduce 库将输入文件分成  M 个数据片段，每个片段 16 -- 64 MB（可以通过可选参数控制）。然后用户程序在集群中创建大量的副本程序。
2. 副本程序中有一个 master，其他程序都是 worker 程序，由 master 分配任务。有 M 个 map 任务和R 个 reduce 任务将被分配，master 将一个 map 任务或者 reduce 任务分配给一个空闲的 worker。
3. 被分配任务 map 任务的 worker 读取相关的输入数据片段（split），解析出  key/value pairs 并传递给 Map 函数，缓存在内存中。
4. 缓存的 k/v pairs 会周期的写入硬盘，通过划分函数划分成 R 个分区，结果保存的位置会返回给 master，master 负责把位置传递给 reduce worker。
5. 当 reduce worker 被通知文件位置时，使用 RPC 读取文件，当读取所有的中间数据的时候，使用中间 keys 对其进行排序，相同的 key 就会被合并。如果数据太大，会使用外部排序。
6. reduce worker 遍历所有排序好的中间数据，对于每个唯一的中间 key 值，reduce worker 程序将这个 key 值和它相关的中间 value 值的集合传递给用户自定义的 Reduce 函数。Reduce 函数的输出附加到所属分区的输出文件。
7. 当所有的  map 和 reduce 任务都完成之后，master 唤醒用户程序。

输出是 R 个文件（每个 reduce 任务输出一个输出文件，命名自由）。一般情况下，不需要将这 R 个输出文件合并成一个文件......

> master 的数据结构

对于每个 map task 和 reduce task，保存有状态（idle，in-progress，completed），以及 worker machine 的标识（对于具有非空闲任务的机器）。

master 是 map 和 reduce 之间传递中间存储区域位置信息的管道。对于每个 完成的 map 任务，master 保存了中间文件的位置、大小。当 map 完成的时候，master 接受到位置和大小的更新信息，然后传递给正在执行reduce 任务的 worker。

> 容错

## Lab



参考链接：

https://xzy57369.github.io/post/3a897789.html

https://zhuanlan.zhihu.com/p/132571559

