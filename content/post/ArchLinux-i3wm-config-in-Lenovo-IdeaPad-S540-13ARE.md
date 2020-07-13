---
date: "2020-07-13T10:38:18+08:00"
title: "ArchLinux i3wm Config (Lenovo IdeaPad S540 13ARE)"
authors: []
categories:
  - 折腾
tags:
  - Lenovo
  - Arch
draft: false
toc: true
typora-root-url: ../../static
---

> 上个月末，准备买一台新的笔记本（老神船太傻大黑粗），选了好几天，决定从联想小新 pro 13 2020 AMD R7 4800u 和 yoga14s 同价版本中挑一个，然后看到多多上面 2020 pro 百亿补贴就直接下单了（实际上过程还是非常曲折的，大概折腾了快小半个月），昨天货到了，检查了一遍，没发现什么很大的问题，主要问题是华星屏幕有点色差和漏光，不过，对我来说应该不是很严重的问题（毕竟我之前用的神船，可能随便一台就足够满意了），所以没换货，毕竟浪费了挺多时间了，而且换货多半还是华星屏幕。检查之后直接双系统，系统选我最爱的 Arch，搭配上熟悉的 i3wm，太舒服了，之前老神舟分辨率看 i3wm 简直瞎眼，所以很长一段时间都是一直用的 gnome，但是还是不行，分辨率太低了导致字体总是很糊，现在看着感官完全不一样。16:10 + 2.5k + 4800u 小新真的太适合 Linux 了，续航也非常可以了，而且也可以调野兽模式、节能模式等等。
>
> 虽然是 AMD CPU，过程还是算比较简单的，wiki 写得很详细，但是比较分散，这里记录一下遇到几个问题，有些是本子特有的，有些是我傻了吧唧的操作导致的。
>
> 还有就是，安装的时候进界面会出现亮线，之后把 grub 分辨率改了就好，这种一般是电压不稳定造成的

<!--more-->

## 时间时间

> 一定一定一定要对好时间才进行之后的操作！！！
>
> 还有就是如果发现 wiki 中文翻译时间比较久了（超过半年或者一年），最好还是对照看下英文版本

官方 wiki: [System time](https://wiki.archlinux.org/index.php/System_time)

操作没啥，如果发现自己解决不了，直接走下面流程也可以（copy from wiki）：

**时间显示的既不是UTC也不是本地时间**

这可能是由多种原因造成的。假如说你的硬件时间使用的是localtime，但`timedatectl`被设置为使用UTC，结果是你的时区偏移设置被应用了两次，导致系统显示的既不是UTC也不是本地时间。

要强制校准你的时钟，并且让系统写入正确的硬件时间，遵循下面几步：

- 设置好[ntpd](https://wiki.archlinux.org/index.php/Ntpd)（不需要将其作为一个服务来运行）；
- 正确设置你的[时区](https://wiki.archlinux.org/index.php/System_time_(简体中文)#时区)；
- 运行`ntpd -qg`，此时在系统和网络进行时间校正时，会忽略本地UTC时间与网络UTC时间之间过大的差异；
- 运行`hwclock --systohc`，将当前的正确UTC时间写入硬件时间。

因为我一开始是直接 ssh 到自己电脑上装的，然后没注意自己电脑的时间是错的（没注意到，而且想着换电脑就没修复）

于是需要导入 keys 的时候出现：

Signature from "User <email@example.org>" is unknown trust, installation failed

这种错误如果一直出现，别怀疑了，就是时间错了

我遇到了之后，弄到失去耐心直接重装需要（昨天晚上 10 点开始重装，搞了两个小时，虽然之后一直熬夜到凌晨 5 点）

## Python Module 的错误

具体报错没了，可以试试：

```sh
sudo pacman -S python3 python-pip python-setuptools
```

差不多就可以解决了

## BackLight

> 屏幕背光调节也是可以的，至于按键背光，因为不需要就没找解决方案（PS. 这键盘真的是垃圾）

和 intel 差不多：

```sh
echo 70 > /sys/class/backlight/amdgpu_bl0/brightness
```

## Bios

> 这个。。。真的有问题

最好还是在 bios 中关闭：

- Fool Proof Fn Ctrl
- Flip To Boot
- Hotkey Modes

这几个，因为有了容易出问题，没有对我不影响

## ALSA

wiki: [ALSA](https://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture)

音频是开箱即用的，遇到问题就看官方 wiki 吧，写得不错的

我基本就配置了一个文件

`.asoundrc`

```
defaults.pcm.card 1
defaults.pcm.device 0
defaults.ctl.card 1
```

## grub 分辨率

> 不改会出现亮线，因为那一瞬间分辨率变化太快了（我是自己这样想的，实测也是这个问题），改成 1280x960 就好

```sh
sudo vim /etc/default/grub

GRUB_GFXMODE=1280x960
```

记得重新生成 grub.cfg

## 一些优化

修改 icons theme，系统托盘会好看一点（实测，nm-applet 这个部件会出现同位重叠，这个问题暂时不想解决）

安装 dunst，通知会好看一点

改了下 alacritty 终端透明

至于 oh-my-zsh、emacs、rofi 配置什么的，随便自己喜好，推荐不要花太多时间折腾（虽然大多数人都需要经历这个过程）

我的 oh-my-zsh 就四个插件，还有两个是自带的，推荐自带 extra 插件，解压缩就只需要 x，不用记一堆东西（虽然还挺好记

emacs 配置就不说了，我单独有个 repo

然后就没了，基本上不用配置，把之前 Dropbox、github 文件夹复制过来，大多数都直接使用，没有区别（才发现我之前装了特别特别多字体）

问题遇到很少，一般都是版本的问题，比如 hugo 0.73 就会报错，0.72 就不会

现在基本上不想配置什么东西了，原来的就挺好看的 :happy:

截图就不放了，反正分辨率真舒服啊，有点像 Mac 上面一样细腻，但是中文字体还是没找到比较适合的，暂时用的还是 Noto Sans，Ubuntu 字体强烈推荐，Ubuntu Mono 不输 Mac Menlo，同时 Ubuntu Regular 也非常好看

## End

> 这篇 blog 就是用小新的键盘敲的（但是 fcitx-rime 重新部署了 4、5 次，垃圾 fcitx），不舒服，也不算特别差，至少比罗技 k380 强（才想起来，一年之前我还是个”键盘“侠，都快忘记自己老本行了）
>
> 这段
>
> > 找评测，看评测，纠结，选好型号，发现没钱，等电脑，中间退货（因为开始是直接买的，后面发现多多补贴少两百，店也是评价值得买评价比较好的店，两百可以买个拓展坞），然后继续等，到货，装系统、重装
>
> 的过程还是很愉快的，至少我这段时间没怎么找工作的事情，算是放松了一段时间吧（PS. 一直都在放松）
>
> 续航也不错，用的时候也基本听不到任何风扇的声音（没有测试过

ArchWiki 这款小新的页面（说明这款还是很不错的）：

https://wiki.archlinux.org/index.php/Lenovo_IdeaPad_S540_13ARE

有切换模式和启动快速启动的命令