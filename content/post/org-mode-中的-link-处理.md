+++
title = "Org-Mode 中的 link 处理"
date = 2020-02-05T13:52:00+08:00
lastmod = 2020-03-25T20:57:00+08:00
tags = ["org"]
categories = ["learn"]
draft = false
author = "abcdlsj"
+++

> Org-Mode link 处理

<!--more-->


## 插入链接 {#插入链接}

`C-c C-l` (org-insert-link)


## 打开链接 {#打开链接}

`C-c C-o` (org-open-at-point)

`设置回车打开链接`

```elisp
(setq org-return-follows-link t)
```


## 额外 {#额外}

下面是两个参考链接

[1](https://brantou.github.io/2017/03/21/just-try/)

[Org Manual](https://orgmode.org/org.html#Hyperlinks)
