+++
title = "Macos配置SICP环境"
date = 2019-01-21
lastmod = 2019-01-21T14:51:14+08:00
tags = ["SICP"]
categories = ["learn"]
draft = false
+++

> 最近想学习一下<<计算机程序的构造和解释>>，毕竟是“魔法书”
> 但是安装 Scheme过程还是很复杂，对于我们来说，老的 MIT-Scheme非常不友好，而且在 win上需要修改一下才能安装上，所以还是还是安装新的 Scheme版本吧(逃。。)
> 但是有一句话`只要思想不滑坡，方法总比困难多`，所以我还是想改造下我的 MIT-Scheme，win上我没装，所以只能在 macOS上演示了(黑苹果就是好)，实际上学习的话还是推荐安装 Racket,毕竟比较新


<!--more-->

## 安装 MIT-Scheme

macOS上非常简单，官网上下载安装就行
[官网](https://www.gnu.org/software/mit-scheme/)

下载不了的话，可以评论给我，我帮你下(虽然说我也是 brew安装的)

或者
```
brew install mit-scheme
```

安装之后就可以输入
```
mit-scheme
```
启动了
## 添加 rlwrap

如果不安装的话就会出现方向健变成这样

![](https://res.cloudinary.com/dc15efw34/image/upload/v1548067065/macOS%20MIT-Scheme/Snipaste_2019-01-21_01-01-08.png)

这个时候我们就需要安装 rlwrap

```bash
brew install rlwrap
```

然后你还需要将这个 [gist ](https://res.cloudinary.com/dc15efw34/raw/upload/v1548046250/macOS%20MIT-Scheme/scheme.txt )保存起来放在你的主目录下，改个好记的名字，比如说我的直接就是 `scheme.txt`,然后你要这样启动

```bash
rlwrap -r -c -f "$HOME"/scheme.txt scheme
```

当然这个很麻烦，所以我们可以封装一下

原谅我菜鸡本质，我只会简单地 shell

## 封装

新建一个文件

```bash
touch scheme
```

然后加权限

```bash
chmod +x scheme
```

写上

```bash
#!/bin/bash
rlwrap -r -c -f "$HOME"/scheme.txt scheme
```

当然你要在哪运行就放在哪

下次运行直接

```bash
bash scheme
```

就行了

## 继续安装 SLIB

去到主页[SLIB](http://people.csail.mit.edu/jaffer/SLIB.html)

然后下载它的源码,解压

解压之后将这个文件夹复制到 `/usr/local/lib/slib`

```bash
cp -r slib /usr/local/lib/slib
cd /usr/local/lib/slib
ls
```

然后看一下应该是这种文件结构

![](https://res.cloudinary.com/dc15efw34/image/upload/v1548005004/macOS%20MIT-Scheme/Snipaste_2019-01-21_01-23-05.png)

然后在这个目录下执行安装文档

```bash
make infoz
make install
```

查询官方文档之后知道这样使用

```bash
scheme -load ${SCHEME_LIBRARY_PATH}mitscheme.init
```

当然这时候也会出现上面的方向键问题

这时候可以复制一份前面的`scheme.txt`文件到 `slib`目录

然后就可以在`slib`目录下

```bash
rlwrap -r -c -f "$HOME"/scheme.txt scheme -load /usr/local/lib/slib/mitscheme.init
```

当然简单点还是在主目录下封装脚本

这个就照着前面来吧

关于 MIT-Scheme使用后面会慢慢写出来
