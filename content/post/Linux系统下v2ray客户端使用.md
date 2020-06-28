+++
title = "Linux 系统下 v2ray 客户端使用"
date = 2018-08-18
lastmod = 2018-08-18T14:51:14+08:00
tags = ["v2ray"]
categories = ["折腾"]
draft = false

toc = true

+++

> V2Ray是一个优秀的开源网络代理工具，可以帮助你畅爽体验互联网，目前已经全平台支持Windows、Mac、Android、IOS、Linux等操作系统的使用。相对起Shadowsocks来说属于后起之秀，在混淆能力、兼容性、速度上有着独到的优点。在目前Shadowsocks、ShadowsocksR停止更新的情况下，多掌握一个活跃项目工具的使用是完全有必要的.

<!--more-->

> 到目前为止，V2Ray项目的状况良好，衍生出一系列GUI客户端，而不再是原先单一的命令行工具。项目更名为Project V，而V2Ray则是Project V项目的内核，如果你对项目本身不感兴趣，依然可以按照原先的方式理解.

这里给大家一个链接

[v2ray 的第三方客户端合集](http://briteming.hatenablog.com/entry/2017/10/21/124645)

上面都是套话,就是说苦逼的ubuntu没有v2ray的GUI客户端，等我有能力一定开发一个(嘿嘿嘿..)

但是没有GUI客户端我们一样可以做一个桌面项，双击运行，文末有写如何配置，这是我的

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534581153/8.18/2018-08-18_16-31-14%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

好的，开始吧！！
## vps上部署

不说了，以前说过的

[使用heroku免费搭建v2ray](https://abcdlsj.github.io/post/%E4%BD%BF%E7%94%A8heroku%E5%85%8D%E8%B4%B9%E6%90%AD%E5%BB%BAv2ray/)(~~很稳，我的现在还没掉，就是速度一般~~ 9月10日实测已经失效了)

[搭建v2ray](https://abcdlsj.github.io/post/%E6%90%AD%E5%BB%BAv2ray/)

## 使用
我使用的桌面环境是ubuntu18.04 LTS

非ubuntu仅作参考(其实是一样的。。)

### 首先我们下载v2ray

推荐官方下载
[v2ray releases](https://github.com/v2ray/v2ray-core/releases/)
这里记得选择合适的版本
一般就选这四个

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534578444/8.18/2018-08-18_15-43-51%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

cpu是Intel架构使用前两个，ARM架构后两个，一般都是前两个
可以使用命令行查看

查看位数
```
getconf LONG_BIT
```
查看CPU型号
```
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
```
选好之后下载，解压得到`v2ray-vx.xx-linux-xx`的文件夹

### 运行

我们需要一个配置文件，这个文件可以自己部署好了之后，使用脚本导出，也可以在Windows 上用软件导出，更可以自己写，对照[官方文档](https://www.v2ray.com/)

配置文件`config.json`移到刚刚下好的文件夹中，把里面的`vpoint_vmess_freedom.json`删除了，这是默认的配置文件.
然后我们在文件夹中打开终端执行
```
sudo ./v2ray
```
这个样子就开启了
![](https://res.cloudinary.com/dc15efw34/image/upload/v1534579248/8.18/2018-08-18_16-00-18%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

这个时候还没完成，我们还需要设定系统代理

### 代理设置

最好使用Chrome和Firefox浏览器，因为我们需要下载插件 SwitchyOmege，Chrome上叫做 Proxy SwichyOmega，应用商店下载就行

打开拓展

新建情景模式

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534580016/8.18/2018-08-18_16-09-07%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534580015/8.18/2018-08-18_16-10-08%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

下面填上这个

```
"local_address":"127.0.0.1"
"local_port":1080
```
新建一个情景模式，这次选择`自动切换模式`

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534580173/8.18/2018-08-18_16-16-02%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534580015/8.18/2018-08-18_16-11-37%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

规则列表网站填这个

> https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt

点击`立即更新情景模式`

然后`应用更改`

然后就可以开始上网了，你以为这就完了吗，没有，下面还有

### 设置桌面项

我们除了像上面那样使用
```
sudo ./v2ray
```
还可以
```
./v2ray --config=/到这个文件夹的路径/config.json
```
我们甚至还可以建立一个桌面项，然后双击启动
```
[Desktop Entry]
Name=V2Ray
GenericName=V2Ray Client
Comment=A platform for building proxies to bypass network restrictions.
Exec=/到你文件夹的路径/v2ray --config=/到你文件夹的路径/config.json
Icon=/到自定义图标图片的路径
Terminal=true
Type=Application
Categories=Network;Internet;

```
然后记得勾选`允许作为程序执行文件`
就可以双击执行V2Ray了(关闭终端即关闭程序).

若要想这个桌面项出现在应用程序列表里,那就把 v2ray.desktop 文件复制到 /usr/share/application里即可(需要root)

这里看我的成果

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534581153/8.18/2018-08-18_16-31-14%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

送上一套图标

![](https://res.cloudinary.com/dc15efw34/image/upload/v1534581151/8.18/2018-08-18_16-32-04%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

[链接(提取码：hc82 )](https://pan.baidu.com/s/1yf4Rkmuy7h65z8XC77SAdg)
