+++
title = "截图+上传+获取链接自动化小工具"
date = 2018-08-15
lastmod = 2018-08-16T14:51:14+08:00
tags = ["Markdown","Python"]
categories = ["learn"]
draft = false

toc = true

+++

> 写博客的人都知道,Markdown 格式的插入图片流程比较复杂，截图->上传->获取链接,那有没有一个小程序能直接完成这三个步骤？这个项目就随之诞生.

<!--more-->

**演示请滑到最下面**

优点:
- 速度快
- 代码简单
- 使用时间戳命名截图，便于管理

缺点：
- 目前代码只支持腾讯云对象存储(因为注册有50G。。，不过很奇怪，速度还没Cloudinary快)

  不过，其实其他的云对象存储也是一样的，代码本身没有多大变化，就修改`上传图片`那部分代码就行
- 因为程序代码简单，所以说可能交互体验上不好(还要手点运行)(没有图形界面)
- 只支持截图上传，也就是复制本地图片，运行程序是不会获得链接的

future:
- 完美而简洁的图像界面
- 云对象自由选择
- 交互体验满分
  - 监听剪贴板，达到完全自动化
  - 拖图直接获取链接(感觉跟cosbrowser好像)

这是自己第一个小程序，勉强算是Version 1.0.0
## 以下简介直接来自README文件(有演示GIF)
一个Markdown实用工具

[Github项目地址](https://github.com/abcdlsj/Markdown-Picture-loopback)

支持系统:

- Windows10 - 支持
- Linux - 不支持
  - ImageGrab is macOS and Windows only
  所以只能使用其他访问剪贴板的方法
- macOS - 支持

### 自动化实现流程：
- 从剪贴板获取截图并保存在本地
- 上传本地图片并返回链接
- 粘贴链接为markdown格式

### 具体实现请看 `upload_get.py` 文件

准备工作
- 我们需要先安装第三方库，这里我给出一个脚本文件，运行能直接安装学习
python所需要的大多数库，具体请看 `library_installation.py` 文件
- 截图工具
  这里我推荐[snipaste](https://zh.snipaste.com/),下载之后F1直接截图
- 我们还可以安装剪贴板工具,便于排查错误.(网上随便下一个就行)

### 使用过程
- F1截图并复制
- 打开 `upload_get.py` (修改其中一些东西，文件中注释详细说明)文件并运行，
- Ctrl+V 粘贴即可

### demo GIF

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534466014/8.17/demo.gif)
