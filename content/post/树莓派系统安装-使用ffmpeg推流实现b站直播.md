+++
title = "树莓派系统安装&使用ffmpeg推流实现b站直播"
date = 2018-09-10
lastmod = 2018-09-10T14:51:14+08:00
tags = ["树莓派","ffmepg"]
categories = ["learn","折腾"]
draft = false
+++

> 树莓派买回来快半个月了,期间实现了很多好玩的玩法,现在才想起来可以写一篇博客记录一下

<!--more-->

## 购买树莓派并安装系统
树莓派购买途径很多,重要的是电源和sd卡,sd卡一定要是c10以上的卡(别问我怎么知道...),当然网上一般是套餐,买了这些都有了,显示屏可以看需求购买

官网下载即可

[树莓派系统下载](https://www.raspberrypi.org/downloads/raspbian/)

我使用的是`2018-06-27-raspbian-stretch.img`

## ssh连接树莓派&远程桌面连接树莓派(没有显示屏)

### 烧录系统
首先把下载好的压缩包解压,得到`.img`文件
使用`Win32DiskImager`或者`Ether`烧录进去
### 开启ssh
新建一个没有后缀的`ssh`文件

新建一个`wpa_supplicant.conf`文件来设置连接wifi
```
country=CN
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="XXX"
    key_mgmt=WPA-PSK
    psk="XXXXX"
    priority=1
}
```
```
#ssid:网络的ssid
#psk:密码
#priority:连接优先级，数字越大优先级越高（不可以是负数）
#scan_ssid:连接隐藏WiFi时需要指定该值为1

// 如果你的 WiFi 没有密码
network={
ssid="你的无线网络名称（ssid）"
key_mgmt=NONE
}
// 如果你的 WiFi 使用WEP加密

network={
ssid="你的无线网络名称（ssid）"
key_mgmt=NONE
wep_key0="你的wifi密码"
}
// 如果你的 WiFi 使用WPA/WPA2加密

network={
ssid="你的无线网络名称（ssid）"
key_mgmt=WPA-PSK
psk="你的wifi密码"
}
```
插入内存卡,开启电源,进入系统

然后以下是几种查看树莓派IP地址的方法

- 路由器开启wifi,可以直接进入后台查看,一般直接就看得到
- Windows开启无线热点

  `cmd`中输入`arp -a`,看到`b`开头的就是了

  也可以下载`Advanced Port Scanner`软件直接扫描就出来了
- 安卓系统开热点
  下载`Termux`终端,里面输入`arp -a`就行了

然后就可以SSH软件登陆了

`默认账户:pi 密码:raspberry`

### 开启远程桌面连接
树莓派系统命令行输入
```
sudo apt-get install -y xrdp
```
安装完成后就可以用Windows自带的远程软件进行远程桌面（用户名：pi 密码：raspberry）
## 直播
### 树莓派编译安装ffmpeg
安装 x264 编码器
```
git clone git://git.videolan.org/x264.git
cd x264
./configure --host=arm-unknown-linux-gnueabi --enable-static --disable-opencl --enable-shared
make
sudo make install
```
安装 ffmpeg(最新版本可能不一样,改一下就行了)

[ffmpeg官网链接](http://ffmpeg.org/download.html)
```
wget http://ffmpeg.org/releases/ffmpeg-4.0.tar.bz2
tar jxvf ffmpeg-4.0.tar.bz2
cd ffmpeg-4.0
./configure --arch=armel --target-os=linux --enable-gpl --enable-libx264 --enable-nonfree
make
sudo make install
```
上面如果想编译快一点可以指定多线程`make -j4`(刚好cpu满载,不能再高了)

这个时候运行命令 ffmpeg 会报错
`ffmpeg: error while loading shared libraries: libx264.so.155: cannot open shared object file: No such file or directory`
输入
```
nano /etc/ld.so.conf
```
然后添加
```
/usr/local/lib
```
然后输入下面命令让设置生效
```
sudo ldconfig
```
然后就可以了
### 开始直播
输入
```
ffmpeg -re -i "直播文件路径" -vcodec copy -acodec aac -b:a 192k -f flv "你的rtmp地址/你的直播码"
```
然后可以写一个脚本自动获取直播码,24小时直播.
