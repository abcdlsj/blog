---
title: "LeetCode Pattern:Dynamic Programming"
date: 2020-04-27T20:49:11+08:00
lastmod: 2020-04-27T20:49:11+08:00
draft: flase
keywords: ["Dynamic Programming"]
description: "LeetCode Pattern dynamic Programming"
tags: ["LeetCode"]
categories: ["learn"]
author: "abcdlsj"
---
> LeetCode 刷题总结之：动态规划（WIP）
<!--more-->



> **动态规划**（英语：Dynamic programming，简称DP）是一种在[数学](https://zh.wikipedia.org/wiki/数学)、[管理科学](https://zh.wikipedia.org/wiki/管理科学)、[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)、[经济学](https://zh.wikipedia.org/wiki/经济学)和[生物信息学](https://zh.wikipedia.org/wiki/生物信息学)中使用的，通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。
>
> 动态规划常常适用于有重叠子问题[[1\]](https://zh.wikipedia.org/wiki/动态规划#cite_note-1)和[最优子结构](https://zh.wikipedia.org/w/index.php?title=最优子结构&action=edit&redlink=1)性质的问题，动态规划方法所耗时间往往远少于朴素解法。
>
> 动态规划背后的基本思想非常简单。大致上，若要解一个给定问题，我们需要解其不同部分（即子问题），再根据子问题的解以得出原问题的解。
>
> 通常许多子问题非常相似，为此动态规划法试图仅仅解决每个子问题一次，从而减少计算量：一旦某个给定子问题的解已经算出，则将其[记忆化](https://zh.wikipedia.org/wiki/记忆化)存储，以便下次需要同一个子问题解之时直接查表。这种做法在重复子问题的数目关于输入的规模呈[指数增长](https://zh.wikipedia.org/wiki/指數增長)时特别有用。

内容来自 [zh-wiki]([https://zh.wikipedia.org/wiki/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92)

> 动态规划的分类

