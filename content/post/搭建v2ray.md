+++
title = "搭建v2ray"
date = 2018-07-26
lastmod = 2018-07-26T14:51:14+08:00
tags = ["v2ray"]
categories = ["折腾"]
draft = false
+++

> 前段时间分享了ssr的搭建方法,现在想做一下搭建v2ray的教程.碰巧do开了一台没被墙的新加坡vps.延迟80左右,用来搭v2ray速度应该可以.

<!--more-->

## 购买服务器&使用密匙登陆

这里不多说了,附上以前的链接[搭建酸酸乳][1]
## 开始搭建

这里我们要使用到的是[v2ray.fun][2]脚本,其实使用脚本对于自己的提升不大,所以可以的话还是自己配置文件或者自己写脚本(说的轻巧w(ﾟДﾟ)w)
### 登陆vps

搭建之前,我们看一下Readme

![v2 readme][3]
发现最好使用Xshell才能导出配置文件

但是我们之前生成的是PuTTY密匙,后缀是.ppk不能直接使用

所以我们需要转换成Xshell的密匙文件

我们打开PuTTYgen,前面的<<搭建酸酸乳>>有提到过

![PuTTYgen][4]

点击`conversions`选择`import key`
![PuTTY1][5]

选择你之前生成的.ppk文件

之后点击`Export OpenSSh key`

![PuTTY2][6]

![PuTTY4][7]

![PuTTY3][8]

打开Xshell([xshell下载地址][9])

点击新建

![xshell新建][10]

在下面这栏,填入主机IP地址

![xshell1][11]

点击`用户身份验证`,然后照下图选择,密匙选择你刚刚生成的,密码不需要填,因为我们导出时没有设置密码

![xshell2][12]

点击`连接`or`确定`

然后就连接成功了

## 脚本搭建
这里我们看一张图![how to choose v2][13]

这是v2的模板

websocket+TLS+Web算是最好方案,但是不一定每个人都适用

### 安装脚本
```
bash -c "$(curl -fsSL https://git.io/vpOeN)"
```
好了是这个样子,速度有点慢

![14][14]
### 配置

输入`v2ray`

我们输入两次`1`后就是下面这个效果

![15][15]

修改加密方式

![16][16]

到这个时候基本上上网足够了

### 更多选择

如果要选择websockets

我们需要绑定域名到主机ip.

解析域名如下,记录值是你的ip

![17][17]

其他的按需求选择
### 获取配置文件

点击`3`后`yyy`就行了

## 使用

### 下载v2ray

有两种方式

#### 直接下载完整客户端

[完整v2ray客户端][18]

#### 最新版本的获取方式
(不会就用上面的完整客户端就好)：点击[Github下载 v2rayN.exe][19] ，同时[Github下载 v2ray-windows-32.zip][20]均下载页面最近Releases的版本。将下载回来的v2ray-windows-32.zip解压，然后将下载回来的v2rayN.exe执行文件复制到v2ray-windows-32.zip解压后的文件夹内(有大约10个文件一起的文件夹即进入到最终一个文件夹)，然后双击运行v2rayN.exe，遇拦截请点更多信息 → 仍要运行

### 连接

#### 然后可以复制上面生成的`vmess链接`

![21][21]

![22][22]
#### 使用生成的二维码

![23][23]

注意是比较密集的那个二维码

#### 手机上使用也差不多

v2rayNG(play store)或者[GitHub下载][24]

打开后点击右上角`+`

然后`扫描二维码`或者`剪贴板导入`都行

#### 这是速度截图

![25][25]

![26][26]

我这是ws伪装后的,如果使用普通tcp或者mkcp速度应该会更快,亲测YouTube有17000左右,虽然YouTube不是很准确但速度还是很快的.我的ws+tls应该没配置好,不然速度应该更快的,但还是可以了,毕竟`这段时间`封锁还是很恐怖的.手机上可能延迟要高些,速度还是可以的.


[1]: https://octopuspalm.top/2018/07/21/%E9%85%B8%E9%85%B8%E4%B9%B3%E6%90%AD%E5%BB%BA/#%E7%94%9F%E6%88%90%E5%AF%86%E5%8C%99
[2]: https://github.com/tracyone/v2ray.fun#%E5%AE%89%E8%A3%85%E5%91%BD%E4%BB%A4
[3]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596644/7.26/Snipaste_2018-07-26_17-02-49.png
[4]: https://res.cloudinary.com/dc15efw34/image/upload/v1532180898/18.7.21/Snipaste_2018-07-21_21-47-45.png
[5]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596643/7.26/Snipaste_2018-07-26_17-09-27.png
[6]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596643/7.26/Snipaste_2018-07-26_17-10-01.png
[7]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596643/7.26/Snipaste_2018-07-26_17-10-27.png
[8]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596643/7.26/Snipaste_2018-07-26_17-11-46.png
[9]: https://pc.qq.com/search.html#!keyword=xshell
[10]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596644/7.26/Snipaste_2018-07-26_17-12-13.png
[11]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596644/7.26/Snipaste_2018-07-26_17-12-45.png
[12]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596644/7.26/Snipaste_2018-07-26_17-14-05.png
[13]: https://res.cloudinary.com/dc15efw34/image/upload/v1532596643/7.26/How_To_Choose.jpg
[14]: https://res.cloudinary.com/dc15efw34/image/upload/v1532598884/7.26/Snipaste_2018-07-26_17-39-41.png
[15]: https://res.cloudinary.com/dc15efw34/image/upload/v1532599416/7.26/Snipaste_2018-07-26_17-40-30.png
[16]: https://res.cloudinary.com/dc15efw34/image/upload/v1532599417/7.26/Snipaste_2018-07-26_17-49-14.png
[17]: https://res.cloudinary.com/dc15efw34/image/upload/v1532598875/7.26/Snipaste_2018-07-26_17-45-18.png
[18]: https://zhshi.gitlab.io/download/v2ray-v3.31.1-windows-32-V2RayN.7z
[19]: https://github.com/2dust/v2rayN/releases
[20]: https://github.com/v2ray/v2ray-core/releases
[21]: https://res.cloudinary.com/dc15efw34/image/upload/v1532598877/7.26/Snipaste_2018-07-26_17-51-05.png
[22]: https://res.cloudinary.com/dc15efw34/image/upload/v1532598878/7.26/Snipaste_2018-07-26_17-51-21.png
[23]: https://res.cloudinary.com/dc15efw34/image/upload/v1532600580/7.26/Snipaste_2018-07-26_18-22-42.png
[24]: https://github.com/2dust/v2rayNG/releases/download/0.5.1/app-universal-release.apk
[25]: https://res.cloudinary.com/dc15efw34/image/upload/v1532598879/7.26/Snipaste_2018-07-26_17-51-43.png
[26]: https://res.cloudinary.com/dc15efw34/image/upload/v1532840330/7.26/Snipaste_2018-07-29_12-57-45.png
