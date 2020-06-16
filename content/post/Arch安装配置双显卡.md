+++
title = "Arch 配置双显卡"
date = 2019-10-08
lastmod = 2020-03-01T16:05:15+08:00
tags = ["Arch", "OS"]
categories = ["折腾"]
draft = false

toc = true

+++

> ArchLinux 双显卡配置，应该多数 Linux 系统都适用，可能细节不一样。

<!--more-->


## 首先开机时禁用 N卡驱动 nouveau {#首先开机时禁用-n卡驱动-nouveau}

开机启动时如果开机黑屏按 `e` 加入以下

```nil
modprobe.blacklist=nouveau
```


## 安装 NVIDIA驱动 {#安装-nvidia驱动}

```shell
sudo pacman -Syu nvidia nvidia-settings xorg-xrandr
```


## 配置 {#配置}


### 修改 grub {#修改-grub}

```shell
sudo nvim /etc/default/grub
```

> GRUB\_CMDLINE\_LINUX\_DEFAULT="loglevel=3 quiet nvidia-drm.modeset=1 acpi\_osi=!
> acpi\_osi=\\"windows 2009\\""


### 修改完之后重新生成 grub.cfg {#修改完之后重新生成-grub-dot-cfg}

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```


### 添加 initramfs 模块 {#添加-initramfs-模块}

[Arch Wiki initramfs](https://wiki.archlinux.org/index.php/Arch%5Fboot%5Fprocess%5F(%25E7%25AE%2580%25E4%25BD%2593%25E4%25B8%25AD%25E6%2596%2587)#initramfs)

在 `/etc/mkinitcpio.conf` 中找到 `MODULES=()` 加入

> nvidia nvidia\_modeset nvidia\_uvm nvidia\_drm


### 重新生成一下 initramfs {#重新生成一下-initramfs}

```shell
sudo mkinitcpio -P
```


### add pacman hook {#add-pacman-hook}

每次更新 `NVIDIA` 都需要重新生成 `initramfs` 可以使用 pacman hook

```shell
sudo nvim /etc/pacman.d/hooks/nvidia.hook
```

```nil
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```


## 显示管理器 {#显示管理器}

[Arch Wiki](https://wiki.archlinux.org/index.php/NVIDIA%5FOptimus%5F(%25E7%25AE%2580%25E4%25BD%2593%25E4%25B8%25AD%25E6%2596%2587)#%25E6%2598%25BE%25E7%25A4%25BA%25E7%25AE%25A1%25E7%2590%2586%25E5%2599%25A8)


## bumblebee {#bumblebee}

如果是 `linux-lts` 是安装 `bumblebee` `bbswitch-dkms`

```shell
sudo pacman -S bumblebee bbswitch
sudo gpasswd -a user bumblebee
systemctl enable bumblebeed.service
```
