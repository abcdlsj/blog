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

> 上个月末，准备买一台新的笔记本（神船太傻大黑粗），选了好几天，决定从联想小新 13 pro 2020 AMD R7 4800u （代号 Lenovo IdeaPad S540 13ARE）和 yoga14s 4800u 同价版本中挑一个，最后选择了 pro 13（实际上过程还是非常曲折的，大概折腾了快小半个月），昨天货到了，检查了一遍，没发现什么很大的问题，主要问题是华星屏幕有点色差和漏光，不过，对我来说应该不是很严重的问题（毕竟我之前用的神船，可能随便一台就足够满意了），所以没换货，毕竟浪费了挺多时间了，而且换货多半还是华星屏幕。检查之后直接双系统，系统选我最爱的 Arch，搭配上熟悉的 i3wm，太舒服了，之前神舟分辨率（1920x1080）简直瞎眼，gnome 也一样字体总是很糊，现在看着感官完全不一样。16:10 + 2.5k + 4800u，续航也非常可以了（超过 6 个小时），而且也可以调野兽模式、节能模式等等。
>
> 虽然是 AMD CPU，过程还是算比较简单的，wiki 写得很详细，但是比较分散，这里记录一下遇到几个问题，有些是本子特有的，有些是我傻了吧唧的操作导致的。
>
> 还有就是，安装的时候进界面会出现亮线，之后把 grub 分辨率改了就好，这种一般是电压不稳定造成的
>
> 本款 ArchWiki 页面：
>
> https://wiki.archlinux.org/index.php/Lenovo_IdeaPad_S540_13ARE
>
> -\- 07-12
>
> 使用两天，添加遇到的几个问题，就写这算了，不想从下面添加了：
>
> - 合盖会冻结，具体表现为输入设备没法使用（i3wm 键盘失效，鼠标可以移动，点击失效），换 linux-zen 内核后解决
>
> - 缩放问题，最好的还是 144
>
>   `vim .Xresources` （PS. 实测鼠标这样设置大小会好一点点）
>
>   ```sh
>   Xft.dpi: 144
>   Xcursor.size:  32 ! 32, 48 or 64 may also be good values
>   ```
>
> - 触控板设置问题，原来是没有手势，比如单击之类的，只能够用触控板本来的点击，感觉不舒服，如果想要简单的就可以像我一样：
>
>   ```sh
>   $ synclient TapButton1=1 TapButton2=3 TapButton3=2
>   ```
>
>   这样就差不多了，更丰富的功能找 wiki
>
> - fcitx 的字体设置：`Noto Sans CJK SC Medium 11`，`Menu Font: Ubuntu Regular 11`
>
>   亲测这样看着会舒服很多，Linux 中文字体渲染没有 Mac 好，设置 Regular 还是会比较虚，Medium 会舒服很多，Ubuntu Regular 就不虚 Mac 了，非常好看
>   
> - 如果发现渲染重叠，去配置 compton
>
> -\- 07-14 :timer_clock: 12:55
>
> 托盘图标还是有点问题，大小不一样，幸好就两个稍微大一点点，找了很多方法，没解决（个人感觉是主题缩放的问题）（07-21 破案了，是字体大小限制的）
>
> 还有就是内存最好划分 2G 给显卡，4G 如果有高级显示需求是需要的，主要不给 2G 我开 Emacs、Goland 太慢，不打游戏 2G 够了
>
> -\- 07-16 :timer_clock: 07:07
>
> 刚刚看了一下 Manjaro Gnome 20 的效果，发现不行，所以最好还是推荐装 i3wm
>
> 实测自己创建 gtk 的缩放主题很麻烦，很不推荐，浪费时间
>
> 如果发现 vlc 打开视频闪烁，去配置 compton，wiki 写了解决方案，但是对我没用，因为只有开全屏才会出现，所以我设置 vlc 为浮动了，而且 mpv 不会闪烁
>
> -\- 07-17 :timer_clock: 19:37
>
> 又使用了两天（还没有需要用到虚拟机和 docker 的地方），个人感觉独显 2G 可能稍稍有点点少，但是也没必要分到 4G，然后暂时也没出现卡死的情况（之前电脑 Chrome 标签开多了会卡死，看来是现在的 CPU 强很多了）
>
> -\- 07-19 :timer_clock: 22:43
>
> i3wm 也可以设置主题的，虽然其实没啥用，和默认比也没啥变化，具体可以 Github 找，我用的是 `base16-google-dark`，之前一直用的 `base16-default-dark`
>
> 镜面屏还是适合对自己颜值有自信的人（不适合我。。。hhh，Emacs 被迫放弃暗色主题）
>
> Typora 推荐使用 Next 主题，自带的中文字体非常适合 Typora
>
> Linux 中文字体渲染真的垃圾，其实有个方法可以适当改善，就是 `lxappearance` 中修改字体为 `Noto Sans CJK SC Medium`，一定是 Medium，这样某些软件界面中文就不会发虚了（也只是某些软件，比如 Typora。。。）
>
> -\- 07-24 :timer_clock: 17:22
>
> 更新 bios 之后是可以用 fn + 功能键的（有音量加减很方便。。。），但是我的背光调节不可以。。。
>
> 经过我的不懈努力，终于找到了究极的字体和 hidpi 配置了
>
> 看文末就可以了
>
> -\- 08-01 :timer_clock: 09:25
>
> 好了，现在换成 Windows 的 Linux 子系统，完结撒花
>
> -\- 08-28 :timer_clock: 17:15
>
> 好了，windows 太难用了，为了图安装快换成 manjaro 了
>
> -\- 08-28 :timer_clock: 14:21
>
> 好了，折腾一天，发现一堆问题，还不如重装前。。。
>
> -\- 08-28 :timer_clock: 21:03

<!--more-->

## 时间时间

> 一定一定一定要对好时间才进行之后的操作！！！
>
> 还有就是如果发现 wiki 中文翻译时间比较久了（超过半年或者一年），还是对照看下英文版本

官方 wiki: [System time](https://wiki.archlinux.org/index.php/System_time)

操作没啥难的，如果发现解决不了，直接走下面流程（copy from wiki）：

**时间显示的既不是UTC也不是本地时间**

这可能是由多种原因造成的。假如说你的硬件时间使用的是localtime，但`timedatectl`被设置为使用UTC，结果是你的时区偏移设置被应用了两次，导致系统显示的既不是UTC也不是本地时间。

要强制校准你的时钟，并且让系统写入正确的硬件时间，遵循下面几步：

- 移动设置好 [ntpd](https://wiki.archlinux.org/index.php/Ntpd)（不需要将其作为一个服务来运行）；
- 正确设置你的[时区](https://wiki.archlinux.org/index.php/System_time_(简体中文)#时区)；
- 运行`ntpd -qg`，此时在系统和网络进行时间校正时，会忽略本地UTC时间与网络UTC时间之间过大的差异；
- 运行`hwclock --systohc`，将当前的正确UTC时间写入硬件时间。

因为我一开始是直接 ssh 到自己电脑上装的（pacman -S openssh，systemctl start sshd），没注意自己电脑的时间是错的（老问题了，想着换电脑就没修复）

于是需要导入 keys 的时候出现：

Signature from "User <email@example.org>" is unknown trust, installation failed

这种错误如果一直出现，别怀疑了，就是时间错了

我遇到了之后，弄到失去耐心直接重装（弄了好几个小时，各种方法都没啥效果，于是昨天晚上 10 点开始重装，2 个小时不到装好，虽然之后一直熬夜玩到凌晨 5 点）

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

> 最好还是自己设置一下

最好还是在 bios 中关闭：

- Fool Proof Fn Ctrl
- Flip To Boot
- Hotkey Modes

这几个对我有影响，自己斟酌

## ALSA

wiki: [ALSA](https://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture)

音频是开箱即用的，但是还是需要稍微配置一下，指定一下输出，遇到问题就看官方 wiki 吧，写得不错的

我基本就配置了一个文件

`sudo vim ~/.asoundrc`

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

修改 icons theme，系统托盘会好看一点

安装 dunst，通知会好看一点

改了下 alacritty 终端透明

至于 oh-my-zsh、emacs、rofi 配置什么的，随便自己喜好，推荐不要花太多时间折腾

我的 oh-my-zsh 就四个插件，还有两个是自带的，推荐自带 extra 插件，解压缩就只需要 x，不用记一堆东西

emacs 配置就不说了，我单独有个 repo

然后就没了，基本上不用配置，把之前 Dropbox、github 文件夹复制过来，大多数都直接使用，没有区别（才发现我之前装了特别特别多字体）

字体可以多装一点点，中文 Noto，界面 Ubuntu Regular，等宽 Ubuntu Mono，Menlo，Hack

问题遇到很少，一般都是版本的问题，比如 hugo 0.73 就会报错，0.72 就不会

现在基本上不想配置什么东西了，原来的就挺好看的 :happy:（事实上，强迫症折腾托盘图标折腾了两天，还没弄好）（PS. 弄好了，看文末）

截图就不放了，反正分辨率真舒服啊，有点像 Mac 上面一样细腻，但是中文字体还是没找到比较适合的，暂时用的还是 Noto Sans，Ubuntu 字体强烈推荐，Ubuntu Mono 不输 Mac，同时 Ubuntu Regular 也非常好看

## 美化设置（i3wm，主要是字体和缩放）

`i3wm` 配置随意，我用的基本是默认

`vim .Xresources` 设置缩放 144，鼠标大小 32

```sh
Xft.dpi: 144
Xcursor.size:  32 ! 32, 48 or 64 may also be good values
```

`qt5ct` 设置 qt 程序字体和图标，对 qt 程序很有帮助，设置字体之后，系统托盘图标也会缩小，我的设置 `Noto Sans CJK SC Medium 10`，如果要生效 `qt5ct` 设置，还需要配置一行东西，看 wiki 吧

设置 qt 字体之后的 obs 界面，如果你也用 obs，可以看到字体效果好很多，而且看到托盘图标大小非常合适

![obs img](/img/obs-show.png)

`lxappearance` 设置 gtk3 程序，看自己喜好，图标我用 `Papirus`，字体用 `Noto Sans CJK SC Medium 10`

通知用 `dunst`，配置使用自带的，`cp -rf /usr/share/dunst/dunstrc ~/.config/dunst`

`Typora` 主题用的 `Next`，截图（右上角是 `dunst` 通知）：

![Typora img](/img/typora-next-theme-show.png)

`Chrome` 字体设置（PS. 08-07 update -\- 前三个设置 `WenQuanYi Micro Hei` 效果比 `Noto Sans CJK SC` 好点）：

![Chrome Fonts Setting](/img/chrome-fonts.png)

显示效果：

![Fonts-Wiki](/img/chrome-fonts-wiki.png)

等宽

![Blog Fonts](/img/chrome-fonts-blog.png)

`alacritty` 字体设置随意，`MesloLGS NF`  不错，支持很多图标，用 `p10k` 必选

其他的就没啥了，字体其实可以直接配置 `fontconfig`，但是我没配，之后可能会去尝试下

总的来说，很多都可以用默认配置，我也只是修改了一部分经常使用的软件配置

## End

> 这篇 blog 就是用小新的键盘敲的（发现 fcitx 有时候会没法输入中文，重新部署了 4、5 次，垃圾 fcitx），键盘不舒服，也不算特别差，至少比罗技 k380  ~~强~~（并没有）
>
> 这段时间
>
> > 找评测，看评测，纠结，选好型号，发现没钱，等电脑，中间退货，然后继续等，到货，装系统、重装
>
> 的过程还是很愉快的，算是放松了一段时间吧（PS. 一直都在放松）
>
> 续航也不错，用的时候也基本听不到任何风扇的声音

ArchWiki 页面：

https://wiki.archlinux.org/index.php/Lenovo_IdeaPad_S540_13ARE

有切换模式和启动快速启动的命令