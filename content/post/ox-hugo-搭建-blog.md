+++
title = "ox-hugo 搭建 blog"
date = 2020-01-13T13:23:00+08:00
lastmod = 2020-03-25T20:52:06+08:00
tags = ["org", "blog", "hugo"]
categories = ["blog"]
draft = false
author = "abcdlsj"
+++

> 前段时间换了 org-page，感觉还是不错的，但是 ox-hugo 更好。

<!--more-->


## 配置hugo {#配置hugo}

网上很多，不多说了


## 安装 ox-hugo {#安装-ox-hugo}

melpa 安装就行了


## 配置 ox-hugo {#配置-ox-hugo}

[org config](https://github.com/abcdlsj/.emacs.d/blob/master/core/init-org.el)


## 生成 blog {#生成-blog}

`C c C e` 加上你要的操作，具体官方有教程


## 坑 {#坑}


### `author` 问题 {#author-问题}

```text
#+author:
#+hugo_custom_front_matter: :author "abcdlsj"
```
