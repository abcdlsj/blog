+++
title = "使用heroku免费搭建v2ray"
date = 2018-08-03
lastmod = 2018-08-03T14:51:14+08:00
tags = ["v2ray"]
categories = ["折腾"]
draft = false

toc = true

+++

> HeroKu 免费搭建 V2Ray

<!--more-->

## 准备工作
- 注册github
  [github注册地址](https://github.com)
- 注册heroku
  [heroku注册地址](https://www.heroku.com)
  选择free的套餐并激活

## 开始搭建

打开github项目地址
  [v2ray-heroku](https://github.com/wangyi2005/v2ray-heroku)

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/Snipaste_2018-08-04_22-56-18.png)

点击部署之后

会跳转到这样一个界面

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/Snipaste_2018-08-04_23-09-23.png)

名称随便

地区选一个就行

uuid默认或者[uuid生成网站](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/Snipaste_2018-08-04_23-09-23.png)生成都行

其他的照着图上填就行

然后点击部署应用

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/9d98062eb915dcfe5e1c8.png)

点击部署之后，稍等一分钟，看到这样的提示就表示代理服务器已经部署好了，点击`View`去看看，出来的页面如果显示Bad Request就代表咱们私人的翻墙服务器已经完全部署好了。你会得到一个名字为你App name的二级域名，例如 https://octopuspalm.herokuapp.com

来到这个界面

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/Snipaste_2018-08-04_23-19-38.png)

我们打开v2rayN(至于如何下载,请看以前教程[v2rayN使用](https://octopuspalm.top/2018/07/26/%E6%90%AD%E5%BB%BAv2ray/#%E4%BD%BF%E7%94%A8))

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/Snipaste_2018-08-04_23-21-50.png)

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/Snipaste_2018-08-04_23-24-32.png)

我的箭头所指的代表需要自己填,ID必须与上面的uuid一致,其他的和图中一样就行

然后就可以使用了.

**使用问题与限制请看[官方文档](https://devcenter.heroku.com/articles/free-dyno-hours)**
