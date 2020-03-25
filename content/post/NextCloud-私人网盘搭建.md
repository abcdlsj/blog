+++
title = "NextCloud私人网盘搭建"
date = 2018-08-22
lastmod = 2018-08-22T14:51:14+08:00
tags = ["NextCloud"]
categories = ["折腾"]
draft = false
+++

> NextCloud搭建

<!--more-->

## 准备工作
### 开一台服务器
不多说了
### 安装宝塔面板
我的是centos系统

> yum install -y wget && wget -O install.sh http://download.bt.cn/install/install.sh && sh install.sh

其他请看[宝塔官网安装文档](https://www.bt.cn/bbs/thread-1186-1-1.html)

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/2.png)

### 域名解析
我想使用二级域名所以我添加这样一条

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/1.png)

## 开始搭建

### 安装组件
打开上面安装好宝塔的那个地址

进去之后照我这样填好安装

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/3.png)

### 宝塔面板添加网站

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/4.png)

### 上传Nextcloud

下载Nextcloud压缩包
- [官网链接]()  (下载速度慢,但是可以下载最新安装包)
- [我的百度网盘链接](https://pan.baidu.com/s/1BaapbonsT0SpgMh2BWVsAw)
(当前最新13.0.5)

打开网站根目录(我的是drive.octopuspalm.top)->删除默认四个文件->上传压缩包->解压->把解压之后文件夹打开->剪切全部->粘贴到根目录->删除之前的空文件夹与压缩包

最后就像这样

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/5.png)

### 设置NextCloud

打开域名

填好(数据库填你刚刚添加网站生成的)

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/6.png)

打开就行

这时候会有安全问题,解决方法在下面

![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/7.png)

## NextCloud安全问题解决

- 根据警告提示需要额外安装 fileinfo 和 opcache。去宝塔面板后台>>软件管理>>php7>>设置，点击安装扩展。

  ![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/9.png)

- PHP 的设置似乎有问题, 无法获取系统环境变量. 使用 getenv(\”PATH\”) 测试时仅返回空结果

  连接 SSH 软件，输入命令编辑(71是我的PhP版本)

  > vi /www/server/php/71/etc/php-fpm.conf

  最后添加

  > env[PATH] = /usr/local/bin:/usr/bin:/bin:/usr/local/php/bin

  重启下服务

  > service php-fpm-71 restart

- 您的数据目录和文件可以从互联网直接访问。.htaccess 文件不起作用。强烈建议您配置 Web 服务器，以便数据目录不再可访问，或都你可以将数据目录移出 Web 服务器文档根目录

  添加伪静态

  > location
  ~ ^/(data|config|.ht|db_structure.xml|README) {
  deny all;
  }

- PHP 的组件 OPcache 没有正确配置。 为了提供更好的性能，我们建议在 php.ini中使用下列设置

  > opcache.enable=1
  opcache.enable_cli=1
  opcache.interned_strings_buffer=8
  opcache.max_accelerated_files=10000
  opcache.memory_consumption=128
  opcache.save_comments=1
  opcache.revalidate_freq=1

  把上面的复制到

  软件管理->PhP7.1管理->配置文件

  倒着找到`Zend Opcache`复制进去(已经有的替换就行)

  ![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/10.png)

- 找到/www/server/php/71/etc/php–fpm.conf，编辑，添加如下内容，保存:
  
  > env[HOSTNAME] = $HOSTNAME
  env[PATH] = /usr/local/bin:/usr/bin:/bin
  env[TMP] = /tmp
  env[TMPDIR] = /tmp
  env[TEMP] = /tmp

- 回到网站根目录，打开config/config.php编辑，添加代码保存:

  > ‘memcache.local’ => ‘OCMemcacheRedis’,
  ‘redis’ => array(
  ‘host’ => ‘localhost’,
  ‘port’ => 6379,
  ),

  ![](https://graph-bed-1256708472.cos.ap-chengdu.myqcloud.com/11.png)

然后基本上就没有问题了....

### 使用测试&心得

这个网盘最好还是只放自己的个人文件,电影之类的还是少放点,毕竟VPS的存储空间,一般都不多

> 我测试过3G的电影上传需要20分钟,使用国内VPS上传2G的电影4分钟搞定

网上有很多使用外置网盘的教程,过段时间我应该会写
