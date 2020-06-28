+++
title = "Centos 开启 Google BBR 加速"
date = 2018-08-28
lastmod = 2018-08-28T14:51:14+08:00
tags = ["Google bbr"]
categories = ["折腾"]
draft = false

toc = true

+++

> 前天搭建了aria2下载站&h5ai在线播放,虽然在线观看速度不错,但还是不尽人意(我的要求比较高),所以我准备开启BBR加速

<!--more-->

前言:
BBR 加速要求更新内核,所以会出现不可预料的结果...(别问我为什么知道...)

所以建议在新的VPS上搭建,其他东西可以重新安装

## 更换内核

### 查看内核
```
uname -r
```

如果是`4.13.0`以下是不能安装BBR加速的
### 更换内核

Centos 更换内核代码如下(安装的应该是最新的内核，现在是4.18.5):
```
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm           #Centos 7
rpm -Uvh http://www.elrepo.org/elrepo-release-6-8.el6.elrepo.noarch.rpm         #Centos 6
```
### 安装内核
```
yum --enablerepo=elrepo-kernel install kernel-ml -y
```
查看内核是否更新
```
rpm -qa | grep kernel
```
![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180828202351.png)

启动内核&重启

这里看到4.18.5是在第2个,所以是1

```
grub2-set-default 1
shotdown -r now
```
查看当前内核
```
uname -r
```
![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180828202824.png)

看到已经更换到了最新内核

## 安装Google BBR
直接执行
```
echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf
sysctl -p
```
## 检查是否成功
```
sysctl net.ipv4.tcp_available_congestion_control
```
看到

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180828203126.png)

输入
```
sysctl -n net.ipv4.tcp_congestion_control
```
看到
![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180828203247.png)

输入
```
lsmod | grep bbr
```
看到
![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180828203326.png)

能看到上面提示,就说明BBR安装成功了,失败请多查询谷歌,或者评论,或者多试几遍

然后我们可以安装其他项目了，速度应该会有明显的提升
