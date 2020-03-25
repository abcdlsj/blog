+++
title = "Linux 文件夹权限设置"
date = 2020-01-13
lastmod = 2020-01-13T23:36:41+08:00
tags = ["linux", "chmod", "os"]
categories = ["learn"]
draft = false
+++

> 我看到文件夹里面文件权限不一，所以我就 `chmod 644 *~然后发现 ~cd` 子文件夹没权限

<!--more-->

网上搜索发现，原来 `cd` 需要可执行文件权限

一般来说 `$HOME` 目录下的文件权限是 644，文件夹权限是 655。

```shell
ls -l ~/
```

```text
总用量 72
drwxr-xr-x  2 abcdlsj abcdlsj 4096 12月 31 12:19 C
drwxr-xr-x  2 abcdlsj abcdlsj 4096  1月 11 21:40 calibre-lib
drwxr-xr-x  2 abcdlsj abcdlsj 4096 12月 20 14:27 D
drwxr-xr-x  2 abcdlsj abcdlsj 4096 12月 20 14:40 Desktop
drwxr-xr-x  2 abcdlsj abcdlsj 4096 12月 20 16:23 Documents
drwx------  9 abcdlsj abcdlsj 4096 12月 25 06:19 doom.d
drwxr-xr-x  2 abcdlsj abcdlsj 4096  1月 13 16:56 Downloads
drwx------  8 abcdlsj abcdlsj 4096  1月 13 10:57 Dropbox
drwxr-xr-x  2 abcdlsj abcdlsj 4096 12月 22 21:55 Gentoo
drwxr-xr-x 15 abcdlsj abcdlsj 4096  1月 13 16:55 GithubPro
drwxr-xr-x  3 abcdlsj abcdlsj 4096  1月  9 00:32 media
drwxr-xr-x  3 abcdlsj abcdlsj 4096 12月 31 18:53 Music
drwxr-xr-x  4 abcdlsj abcdlsj 4096  1月 13 12:39 Pictures
-rw-r--r--  1 abcdlsj abcdlsj   17  1月  8 14:26 README.md
drwxr-xr-x  2 abcdlsj abcdlsj 4096  1月  6 20:41 Scripts
drwxr-xr-x  2 abcdlsj abcdlsj 4096 12月 20 16:23 Videos
drwxr-xr-x  3 abcdlsj abcdlsj 4096  1月  6 10:58 VirtualBox VMs
drwxr-xr-x  2 abcdlsj abcdlsj 4096  1月  4 15:38 vmshare
```
