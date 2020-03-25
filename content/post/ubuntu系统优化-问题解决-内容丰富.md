+++
title = "ubuntu系统优化&问题解决(内容丰富)"
date = 2018-07-29
lastmod = 2018-07-29T14:51:14+08:00
tags = ["Linux"]
categories = ["折腾"]
draft = false
+++

> Ubuntu（国际音标：/ʊˈbʊntuː/，uu-buun-too)是以桌面应用为主的Linux发行版，Ubuntu由Canonical公司发布，他们提供商业支持。它是基于自由软件，其名称来自非洲南部祖鲁语或科萨语的“ubuntu”一词（译为乌班图），意思是“人性”、“我的存在是因为大家的存在”,是非洲传统的一种价值观。

来源.维基百科
<!--more-->

## 安装ubuntu

网络上很多，不再赘述（其实是我也搞不太明白分区）

## 系统优化（安装系统后可以从头做一遍）

1.切换软件源
默认中国服务器，我们把它切换成aliyun的。
在设置--软件和更新里--下载自--其他站点--中国--http://mirrors.aliyun.com/ubuntu
![选区4][1]
2.把所有软件源和软件更新到最新

>sudo apt-get update
  sudo apt-get upgrade

3.卸载掉系统自带的不好用或者基本不用的软件

 - 卸载掉Amazon软件

>sudo apt-get remove unity-webapps-common

 - 卸载掉一些基本不使用的软件和游戏
```
 sudo apt-get remove thunderbird（邮箱）
 sudo apt-get remove  totem （视频播放器）
 sudo apt-get remove rhythmbox（音乐播放器）
 sudo apt-get remove empathy
 sudo apt-get remove brasero
 sudo apt-get remove simple-scan
 sudo apt-get remove gnome-mahjongg
 sudo apt-get remove aisleriot
 sudo apt-get remove gnome-mines
 sudo apt-get remove transmission-common
 sudo apt-get remove gnome-orca
 sudo apt-get remove webbrowser-app
 sudo apt-get remove gnome-sudoku
```
- 卸载掉libreoffice软件（有wps）

>sudo apt-get remove libreoffice-common

3.安装ubuntu下经常使用的好用的软件

  - 安装VIM 编辑器

>sudo apt-get install vim

- 安装搜狗输入法
[搜狗Linux下载地址][2]
deb文件包可以直接安装
或者cd 到安装包目录
执行`sudo dpkg -i +包名`

- 安装wps
[WPS Linux下载地址][3]
同上

- 安装网易云音乐播放器
[网易云下载地址][4]
选择Linux版
然后同上

- 安装桌面美化工具unity-tweak-tool

> 作为一款美化的辅助工具，ubity-tweak同样提供了很多美化设置，尤其是针对Unity桌面的美化，让我们方便的选择主题、图标、鼠标指针、字体、窗口特征、启动器外观等功能。

直接终端输入`sudo apt-get install unity-tweak-tool`

- 安装Sublime Text 3
```
sudo add-apt-repository ppa:webupd8team/sublime-text-3
sudo apt-get update
sudo apt-get install sublime-text
```
linux上的sublime是不支持中文显示的，所以我们需要做一些修改
参考
[解决Ubuntu 16.04下Sublime Text 3无法输入中文的问题][5]

4.美化

- Flatabulous主题＆图标

扁平化设计，非常漂亮，也是我最喜欢的主题，效果图
![选区６][6]

安装主题
```
sudo add-apt-repository ppa:noobslab/themes
sudo apt-get update
sudo apt-get install flatabulous-theme
```
安装图标
```
sudo add-apt-repository ppa:noobslab/icons
sudo apt-get update
sudo apt-get install ultra-flat-icons
```
打开Unity Tweak Tool修改（直接终端输入`unity-tweak-tool`打开）

修改图标和主题

再换一张好看高清壁纸即可

## 科学上网工具(持续更新)
### 安装SS-qt5（免费但是需要自己搭建ss或者购买账号）
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
安装过后怎么使用不再多说，可以看看我博客其他几篇搭建ssr/v2ray文章，没有写ss

### 安装蓝灯(付费，但是每月500M高速免费，用来查资料还可以)

[蓝灯Github项目地址][7]

### 萤火虫（高速付费，不付费很慢，只能浏览网页）

[firefly项目地址][8]
### V2ray使用
请看

[Linux下V2ray客户端使用](https://octopuspalm.top/2018/08/18/Linux%20%E7%B3%BB%E7%BB%9F%E4%B8%8Bv2ray%E5%AE%A2%E6%88%B7%E7%AB%AF%E4%BD%BF%E7%94%A8/)

## 使用过程出现问题&解决方法（持续更新）
### 开机启动小键盘
ubuntu系统有时候不开启小键盘，所以我们想让他开机自动开启
1.安装软件
>sudo apt-get install numlockx

2.编辑  /etc/rc.local 文件，使用vi / vim 命令编辑，如
>sudo vim /etc/rc.local

3.在文件看到 exit 0 这行前面加上以下内容
>if [-x /usr/bin/numlockx ]; then
  numlockx on

4.重启
>sudo shutdown -r now

5.解决啦。。。。

### 无法连接网络问题

#### 基本判断是否有网卡驱动
1.输入`ifconfig`指令，发现只有`lo：localhost`，并没有eth0网卡
![终端1][9]
2.尝试激活网卡，输入指令`ifconfig eth0 up`，发现出现`eth0：no such device`，代表没有此设备
3.输入`ifconfig a`,如果还是没有eth0，代表没有网卡驱动，需要自行安装，请按照下面方法安装

#### 自己安装网卡驱动

1.查看自己网卡驱动

下一个鲁大师，或者去京东搜自己电脑型号然后看评论，一般会有鲁大师截图(手动狗头)
或者如下命令

| 命令                             | 代码                              |
| -------------------------------- | --------------------------------- |
| 查看网卡生产厂家和型号的基本信息 | `lspci`  |
| 查看网卡生产厂家和型号的详细信息 | `lspci -vvv` |
| 查看网卡驱动                     | `lspci -vvv` or `lsmod` |
| 查看网卡驱动版本                 | `modinfo/ethool/ethool -i eth3` |
| 查看网络接口队列数               | `ethtool -S eth0` |

2.bcm网卡
这个就很简单了
网上一大堆，因为bcm可以直接两行命令行安装

3.瑞昱网卡安装
如果网卡型号是rtl8188ee/rtl8192c/rtl8192ce/rtl8192cu/rtl8192de/rtl8192ee/rtl8192se/rtl8723ae/rtl8723be/rtl8723com/rtl8821ae/rtl8822be可以使用这个方法

```
sudo apt update
sudo apt install git
git clone https://github.com/rtlwifi-linux/rtlwifi-next
cd rtlwifi-next
make
sudo make install
sudo modprobe rtl8822be
```
最后是自己的网卡型号,自行修改
附上[内核过低失败解决方案][10]
详情请看[参考链接][11]

其他网卡型号

我的网卡是RTL8111/8168/8411
只能手动安装,先去官网下载驱动
[网卡RTL8111/8168 官方驱动链接页][12]
解压，查看readme 文件
发现可以直接使用安装脚本，所以输入
```
sudo su
./autorun.sh
```
安装之后，应该就可以了.主要是去看Readme文件（配合翻译使用）
（官方文档最重要，其次去网上搜别人怎么安装的)
推荐链接[安装Realtek RTL8111/RTL8168网卡驱动详解（error~eth0:no such device）][13]

### 无线上网问题

1.查看无线网卡型号
终端输入`lspci`
找到下面这行
![终端2][14]
2.如果是Broadcom Corporation....系列网卡的话，就很简单了，网上一搜全是这个教程
推荐[Ubuntu 16.04下无线网卡无法正常连网][15]
3.Intel网卡
网上应该有教程，附上[Linux的intel驱动地址][16]
3.如果是Intel Corporation Device 24fb网卡的话，请查看你的系统是否在17.10以上，因为是intel这款网卡太新，ubuntun16.04没有集成这款硬件的驱动（我就是这款网卡，好恨自己为啥没看到这个）
详情查看[ubuntun16.04不支持intel的最新网卡，升级到17.10后解决][17]
4.还可以试试这种方法（虽然我没有作用）
[Ubuntu16.04无法连接Wifi解决方案 ][18]


  [1]: https://res.cloudinary.com/dc15efw34/image/upload/v1532859883/%E8%A7%A3%E5%86%B3ubuntu%E4%B8%80%E7%B3%BB%E5%88%97%E9%97%AE%E9%A2%98/%E9%80%89%E5%8C%BA_004.png
  [2]: https://pinyin.sogou.com/linux/
  [3]: http://community.wps.cn/download/
  [4]: https://music.163.com/#/download
  [5]: https://blog.csdn.net/oppo62258801/article/details/78996046
  [6]: https://res.cloudinary.com/dc15efw34/image/upload/v1532862219/%E8%A7%A3%E5%86%B3ubuntu%E4%B8%80%E7%B3%BB%E5%88%97%E9%97%AE%E9%A2%98/%E9%80%89%E5%8C%BA_006.png
  [7]: https://github.com/getlantern/lantern
  [8]: https://github.com/yinghuocho/firefly-proxy
  [9]: https://res.cloudinary.com/dc15efw34/image/upload/v1532842889/%E8%A7%A3%E5%86%B3ubuntu%E4%B8%80%E7%B3%BB%E5%88%97%E9%97%AE%E9%A2%98/%E7%BB%88%E7%AB%AF_001.png
  [10]: https://blog.csdn.net/ytusdc/article/details/79675410
  [11]: https://blog.csdn.net/hustcw98/article/details/79118794
  [12]: http://www.realtek.com.tw/downloads/downloadsView.aspx?Langid=1&PNid=13&PFid=5&Level=5&Conn=4&DownTypeID=3&GetDown=false#2
  [13]: https://blog.csdn.net/qljessica123/article/details/44957527
  [14]: https://res.cloudinary.com/dc15efw34/image/upload/v1532856653/%E8%A7%A3%E5%86%B3ubuntu%E4%B8%80%E7%B3%BB%E5%88%97%E9%97%AE%E9%A2%98/%E7%BB%88%E7%AB%AF_002.png
  [15]: https://www.linuxidc.com/Linux/2017-10/147366.htm
  [16]: https://wireless.wiki.kernel.org/en/users/drivers/iwlwifi
  [17]: http://www.mamicode.com/info-detail-2103508.html
  [18]: https://blog.csdn.net/weixin_41762173/article/details/79480609
