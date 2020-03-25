+++
title = "Linux好用的百度网盘客户端"
date = 2018-08-15
lastmod = 2018-08-15T14:51:14+08:00
tags = ["baidu"]
categories = ["折腾"]
draft = false
+++

> 网盘下载每个人都是必不可少的

<!--more-->

百度网盘在Linux 下的下载方式有很多
（小文件使用第一种方法就行，大文件推荐第二种）
- 脚本提取直链+多线程下载(比较多随便列举几个，使用方法网上搜就行)
    - aria2
    - wget
    - axel
- 网盘客户端
    - [BaiduPCS-Go](https://github.com/iikira/BaiduPCS-Go)
    - [baidu-netdisk-downloaderx](https://github.com/b3log/baidu-netdisk-downloaderx)(推荐，速度快，操作简单)
    - [bypy](https://github.com/houtianze/bypy)

## 今天就介绍使用 BND来下载网盘文件
  > BND（Baidu Netdisk Downloader）是一款图形界面的百度网盘不限速下载器，支持 Windows、Linux、Mac.

[详情地址](https://hacpai.com/article/1524460877352#toc_h3_11)

## 特征
- 简单友好的图形用户界面
- 支持 Windows、Mac、Linux
- 通过 Cookie [BDUSS] 登录，无需担心密码泄漏
- 通过数据分片并结合 Aria2 突破百度云限速支持断点续传

## 下载
- [下载地址](https://share.weiyun.com/57zViCm)
下载合适版本后解压
- 点击`Bnd` 图标

## 使用

打开后是这样子
![](https://res.cloudinary.com/dc15efw34/image/upload/v1534343895/8.15/2018-08-15_22-34-28_%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)
- 去注册一个账号(黑客派论坛)
    - [官网链接](https://hacpai.com/)
    - 这是我的[邀请链接](https://hacpai.com/register?r=abcdlsj)(我们都会获得200积分，下载文件需要积分，1G/10积分)

- 获取 BDUSS
    获取 BDUSS方法有好几种
    我介绍一种很简单的方法

    谷歌浏览器新建一个无痕窗口

    然后在无痕窗口打开[百度](https://www.baidu.com/)

    选择`登陆`

    然后点击左上角绿色小锁标志,点击 Cookie

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534345091/8.15/2018-08-15_22-53-14_%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

然后

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534345091/8.15/2018-08-15_22-57-14_%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

把复制进来的BDUSS填进去

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534343908/8.15/2018-08-15_22-36-11_%E7%9A%84%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

点击`登陆`

然后就开始愉快的下载吧！！！
速度还是很快的

## 注意事项
- 要下载的文件放在网盘根目录
- 关闭客户端之后需要重新登陆(BDUSS不需要再次填写)(这算是小问题吧)
- 下载需要积分，但是花的很少，偶尔去论坛签签到就足够了，而且初始500积分，邀请一个人，双方200积分，还是很不错的.
