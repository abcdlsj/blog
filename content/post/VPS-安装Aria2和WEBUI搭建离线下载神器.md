+++
title = "VPS搭建aria2下载&使用h5ai实现在线播放"
date = 2018-08-26
lastmod = 2018-08-26T14:51:14+08:00
tags = ["aria2","h5ai"]
categories = ["折腾"]
draft = false

toc = true

+++

> 好久没有更新博客了,原因是最近到了一个"小玩具" -- 树莓派,现在正在研究当中

<!--more-->

> 自己手上还有闲置的小鸡,刚好拿来搭建 aria2下载站

## 准备工作
开一台VPS,我装的系统是 Centos7.5(推荐,因为比较新)

推荐国内大带宽VPS,搭建下载站后速度不错

**经过我2天使用,我发现还是先开启Google BBR加速再执行下面步骤最好**

下面是开启Google BBR链接

[开启BBR加速](https://abcdlsj.github.io/post/centos-%E5%BC%80%E5%90%AFgoogle-bbr%E5%8A%A0%E9%80%9F/)

## 安装下载工具
### 安装aria2
> yum install epel-release
  yum install aria2

这个时候下载有以下命令

```
#单个文件下载
aria2c http://example.org/mylinux.iso
#从两个来源（更多也可以）
aria2c http://a/f.iso ftp://b/f.iso
#BitTorrent
aria2c http://example.org/mylinux.torrent
#BitTorrent Magnet URI
aria2c 'magnet:?xt=urn:btih:248D0A1CD08284299DE78D5C1ED359BB46717D8C'
#Metalink
aria2c http://example.org/mylinux.metalink
#文本文件uri.text中的链接(URI)
aria2c -i uri.txt
#显示种子中包含了哪些文件
aria2c -S bit.torrent
```
### 配置aria2

#### 开启RPC远程管理命令
```
aria2c --enable-rpc --rpc-listen-all=true --rpc-allow-origin-all -c -D
```
说明: 这个命令在VPS上运行即可开启rpc服务,可以用多种方式进行远程管理,但貌似不会保存配置文件,重启后会丢失之前的下载记录

#### 让配置文件不丢失

用winscp新建以下文件【/root/aria2.conf】，文件内容如下:

**'#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改**

**被注释的选项填写的是默认值, 建议在需要修改时再取消注释**

```#允许rpc
enable-rpc=true
#允许所有来源, web界面跨域权限需要
rpc-allow-origin-all=true
#允许非外部访问
rpc-listen-all=true
#RPC端口, 仅当默认端口被占用时修改
#rpc-listen-port=6800
#用户名
rpc-user=admin
#密码
rpc-passwd=octopuspalm
###速度相关
#最大同时下载数(任务数), 路由建议值: 3
max-concurrent-downloads=5
#断点续传
continue=true
#同服务器连接数
max-connection-per-server=5
#最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要
min-split-size=20M
#单文件最大线程数, 路由建议值: 5
split=10
#下载速度限制 0 不限制
max-overall-download-limit=0
#单文件速度限制
max-download-limit=0
#上传速度限制
max-overall-upload-limit=0
#单文件速度限制
max-upload-limit=0
#断开速度过慢的连接
#lowest-speed-limit=0
#验证用，需要1.16.1之后的release版本
#referer=*
###进度保存相关
input-file=/root/aria2.session
save-session=/root/aria2.session
#定时保存会话，需要1.16.1之后的release版
#save-session-interval=60
###磁盘相关
#文件保存路径, 默认为当前启动位置
dir=/var/www/html/
#文件缓存, 使用内置的文件缓存, 如果你不相信Linux内核文件缓存和磁盘内置缓存时使用, 需要1.16及以上版本
#disk-cache=0
#另一种Linux文件缓存方式, 使用前确保您使用的内核支持此选项, 需要1.15及以上版本
#enable-mmap=true
#文件预分配, 能有效降低文件碎片, 提高磁盘性能. 缺点是预分配时间较长
#所需时间 none < falloc ? trunc << prealloc, falloc和trunc需要文件系统和内核支持
file-allocation=prealloc
###BT相关
#启用本地节点查找
bt-enable-lpd=true
#添加额外的tracker
#bt-tracker=<URI>,…
#单种子最大连接数
#bt-max-peers=55
#强制加密, 防迅雷必备
#bt-require-crypto=true
#当下载的文件是一个种子(以.torrent结尾)时, 自动下载BT
follow-torrent=true
#BT监听端口, 当端口屏蔽时使用
#listen-port=6881-6999
#aria2亦可以用于PT下载, 下载的关键在于伪装
#不确定是否需要，为保险起见，need more test
enable-dht=false
bt-enable-lpd=false
enable-peer-exchange=false
#修改特征
user-agent=uTorrent/2210(25130)
peer-id-prefix=-UT2210-
#修改做种设置, 允许做种
seed-ratio=0
#保存会话
force-save=true
bt-hash-check-seed=true
bt-seed-unverified=true
bt-save-metadata=true
#定时保存会话，需要1.16.1之后的某个release版本
#save-session-interval=60
```
然后执行
```
aria2c --conf-path=/root/aria2.conf -D
```
加入开机自动启动

先执行
```
yum install -y vim
vi /etc/rc.d/rc.local
```
然后加入下面这一行
```
aria2c --conf-path=/root/aria2.conf -D &
```

### 安装WEBUI面板
其实可以选择更加简洁的AriaNg面板，这里就不说了，步骤都差不多
#### 安装http服务
需要开放80端口，一般都是开启的

如果使用iptables

就需要自己开启

参考[网址](https://www.awaimai.com/1650.html)

执行
```
yum -y install httpd
chkconfig --levels 235 httpd on
service httpd start
```

#### 下载 WEBUI压缩包

[Github地址](https://github.com/ziahamza/webui-aria2)

或者直接下载我的(速度快)
[下载地址](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/webui-aria2-master.zip)

#### 上传

解压并上传到`/var/www/html`

改名为`webui`

设置权限
```
chmod 755 /var/www/html/webui
```

### 使用

打开
`ip地址/webui`
就行

设置RPC密码的请在设置里面填写密码和用户

这是我的1M带宽vps下载速度，已经很快了
![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180827140322.png)

这是我的do VPS

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/20180827180420.png)

## 安装h5

### 安装PHP
```
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm --force --nodeps

yum install php70w

service httpd restart
```

### 下载 h5ai &上传

[Github地址](https://github.com/lrsjng/h5ai)

或者

[下载地址](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/_h5ai.tar)(速度快)

上传到`var/www/html/`跟之前的webui文件一起

改名为`_h5ai`

### 修改apache 配置文件
修改`/etc/httpd/conf/httpd.conf`文件，在最后面增加以下内容，注意替换相关信息(IP 换你的 IP)
```
ServerName IP:80
NameVirtualHost IP
<VirtualHost IP>
ServerAdmin admin@octopuspalm.top
DocumentRoot /var/www/html/
ServerName IP
DirectoryIndex index index.html index.php /_h5ai/public/index.php
</VirtualHost>
```
然后执行
```
service httpd restart
```
重启下apache服务

输入你的IP地址访问

但是这样每个人都可以通过你的IP来访问，所以我们要限制访问

下面我们添加用户和密码

### 限制查看

我们可以加入这样的代码

打开`/var/www/html/_h5ai/public/index.php`

在`<?php`之后加入
```
session_start();
if(isset($_POST['password']) && $_POST['password'] == '你的密码'){
    $_SESSION['ok'] = 1;
    header('location:?');
}
if(!isset($_SESSION['ok'])){
    exit('
        <form method="post">
            密码：<input type="password" name="password" />
            <input type="submit" value="登陆" />
        </form>
    ');
}
```
上面的密码还是要好好保存
可以使用一个生成密码的小脚本
```python
import random
import string
def Makepass(length=15, chars=string.ascii_letters+string.digits+'string.punctuation'):
        return ''.join([random.choice(chars) for i in range(length)])

if __name__=='__main__':
    print(Makepass())
```

出现乱码情况

下载文件->记事本另存为UTF-8编码->上传文件->解决

或者WinSCP直接修改文件编码

## 以后下载文件到`/var/www/html/`目录就可以查看了
