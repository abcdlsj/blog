---
date: "2020-06-17T23:00:00+08:00"
title: "BoltDB Analysis and Implementation（WIP）"
authors: []
categories:
  - learn
tags:
  - BoltDB
  - DataBase
draft: false
toc: true
typora-root-url: ../../static
---

> BoltDB 分析与实现

> <!--既然准备写 k/v 数据库，那么肯定要学习开源项目，关于语言的选择（C++ / Golang），我的想法是：语言肯定不是问题，但是语言选择同样很重要（没错，就是看不起 Java！（背地里吹爆）），我很看好 Golang，很多公司也逐渐的抛弃了 C++，转而拥抱了 Golang。就我的日常学习和使用方面来说（实际上 C++ 没有深入，Golang 也是才学不久，我的看法可能会非常浅薄），我觉得 Golang 能够让人更好的去组合，去体会项目的设计和架构，而不是用 C++ 费心的去掌握一切资源（虽然说 RAII 机制能够很好的管理资源，但是同样也增加了难度）。-->
>
> <!--我第一次体会程序设计的魅力是 SICP 带给我的，体会到黑盒抽象，也学习到如何降低程序的“复杂度”（看了前三章不到，以后慢慢看），而我学习 C/C++ 的时候并没有体会到类似的感觉，Golang 带给我的感觉和 Scheme 很相似，虽然 Scheme 简单很多（在这里，我要吹一波，SICP 天下第一！等我稳定下来（感觉最好的时间段还是找到工作到入职的那段时间），我一定用自己实现的 lisp 方言来刷 SICP（这 Flag NB！！！））-->
>
> <!--既然我选择 Golang 来写 k/v 数据库，那么肯定需要读老少咸宜的 `boltDB`  啦（好用稳定简单），预计花费十天时间分析源码（3000 + 行，差不多这个月末，希望不要打脸，我还一堆大半个月的坑都没去填，中间还有考试、课设验收、报告，感觉会被打脸打得很惨。。。），与此同时自己的 k/v 数据库也应该开始动工了（参考 boltDB，没有去实习，项目不能少），之前还打算写 SQLite lite 的（C 语言，有 tutorials，可以照着写，虽然不全。。。），应该会过段时间才去试试（也可能不写了。。。），项目可选的其实感觉很多的，但是好几个自己建好 Repo，然后就没然后了，一直挂着 WIP （Github 点开，一堆 WIP，不知道的还以为这是啥暗语，笑死。。。）-->
>
> <!---\- 06-17 :timer_clock: 23:59-->
>
> <!--果然。。。这两天着实有点忙（虽然我很划水，但是我还是感觉到了压迫感，很不舒服，什么事情都不想做），下周会更加忙，所以抓紧！！！-->
>
> <!--改成  `draft: false` 了-->
>
> <!---\- 06-21 :timer_clock: 21:19-->
>
> <!--事实证明，我没时间了！！！，我这几天看了下源码（准确用词，看了两个多小时），23、24 两天，基本和图像课设作伴（实际上没弄出来，然后从 Github 上直接复制了一个特征脸的程序，然后答辩的时候，我直接说 Windows 环境没配好，把运行截图给老师看了之后，剩下的就是随机应变了，傅里叶描述子直接说没看，然后可能老师累了就没说啥），昨天端午节，停电了半天（课设那天停电，端午停电，还是两个不同片区）。晚上交 DSP 实验。。。我下次还是不立 Flag 了，每次都完不成，计划赶不上变化。。。还有就是这两天也准备开始投递提前批了（之前找实习，放弃，然后躺到了现在，希望提前批不要再懒下去了），好好复习下基础，然后就开始无尽的面试当中，没有实习，不知道秋招会不会白给，也没有好的项目，源码也没读，完全没有竞争力啊！（多亏基本没人知道我的博客。。。）-->
>
> <!---\- 06-26 :timer_clock: 13:16-->
>
> <!--拖延了好多事情，不想动啊。。。-->
>
> <!---\- 06-29 :timer_clock: 16:31-->
>
> <!--k/v 数据库，我现在倾向于组合现有的 Raft 和 BoltDB，而不是从开始就造轮子（重复造轮子不可取，容易迷失自己本来目的）。-->
>
> <!---\- 07-02 :timer_clock: 10:15-->
>
> <!--Flag 看看就好。。。我是不相信我自己了 :cat: 
>
> <!---\- 07-10 :timer_clock: 19:36-->
>
> <!--暂时不想将这篇 blog 放出来了，因为自己一而再再而三的拖延已经让我无法面对任何有稍许困难的事情，我只想着安逸-->
>
> <!---\- 07-15 :timer_clock: 19:47-->

## boltDB 介绍

> 特点就是简单（

特点：

- 单机数据库
- k/v 存储，B+ 树索引
- 支持事务
- mmap 缓存，逻辑简单

源码结构：

- 存储和缓存：page.go, freelist.go, blot_xxx.go
- 数据和索引：node.go
- 桶：bucket.go, cursor.go
- 事务：tx.go
- API：db.go

## 存储与缓存

## page

在 bolt 当中，文件被组织成 PAGE_SIZE 大小的 page，结构是：

```go
// page size(4096 bytes)
type page struct {
	id       pgid    // page id
	flags    uint16  // 区分 page 类型的标识
	count    uint16  // page data 中的数据个数
	overflow uint32  // 如果单个 page 大小不够，会分配多个 page，overflow 记录溢出
	ptr      uintptr // 指向 page 数据的内存地址
}
```

page 有四种类型

```go
// page 共有四种类型
const (
	branchPageFlag   = 0x01 // b+ tree node
	leafPageFlag     = 0x02 // b+ tree leaf
	metaPageFlag     = 0x04 // db mete page
	freelistPageFlag = 0x10 // db free page
)
```

创建的时候，初始化成 4 个 page

| metaPage | metaPage | freelistPage | leafPage |



node:

B+ 树单个节点，访问节点首先将 page 的内容转换为内存中的 node，每个 node 对应一个或者多个连续的 page。

Bucket: 每个 Bucket 都是完整的 B+ 树，所有操作都是针对 Bucket

查找过程：

1. 首先找到 Bucket 的根节点，也就是 B+ 的根节点 page id；
2. 读取对应的 page，转化为内存中的 node；
3. 若是 brabch node，则根据 key 查找子节点 page id;
4. 重复 2、3 直到找到 leaf node，返回 node 对应的 val;

查找过程：

## references

https://github.com/qw4990/blog/blob/master/database/boltDB/README.md

https://youjiali1995.github.io/storage/boltdb/

https://github.com/ZhengHe-MD/learn-bolt