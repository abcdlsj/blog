+++
title = "Linux 定时任务"
date = 2020-01-29T19:45:00+08:00
lastmod = 2020-05-14T16:19:30+08:00
tags = ["Linux"]
categories = ["learn"]
draft = false
author = "abcdlsj"
+++

> Linux 上有时候需要定时启动某些任务，运行脚本，或者提交什么东西

<!--more-->


## systemd/Timers {#systemd-timers}

[systemd/Timers ArchWiki](https://wiki.archlinux.org/index.php/Systemd/Timers%5F(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

```shell
systemctl list-timers
```


## cronie {#cronie}

[cronie ArchWiki](https://wiki.archlinux.org/index.php/Cron%5F(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

```shell
sudo pacman -S cronie
```

```shell
crontab -e
```

编辑后使用 `crontab -l` 查看

```shell
30 19 */2 * * /home/abcdlsj/Scripts/leetcode.sh
```

格式是

```nil
<分钟> <小时> <日> <月份> <星期> <命令>

分钟 值从 0 到 59.
小时 值从 0 到 23.
日 值从 1 到 31.
月 值从 1 到 12.
星期 值从 0 到 6, 0 代表星期日.
```

当 XX 设置为 `*` 时，代表每 XX （小时，分钟。。。） 运行

当设置为 `a-b` 时，代表从 a-b 执行

当设置为 `*/n` 时，代表每 n XX 执行

当设置为 `a.b.c...` 时，代表第 a b c 小时

查看运行（运行的是我的 leetcode 爬虫）

```nil
$ systemctl status cronie.service
● cronie.service - Periodic Command Scheduler
	 Loaded: loaded (/usr/lib/systemd/system/cronie.service; enabled; vendor preset: disabled)
	 Active: active (running) since Wed 2020-01-29 19:37:11 CST; 5min ago
   Main PID: 410 (crond)
	  Tasks: 1 (limit: 18994)
	 Memory: 27.5M
	 CGroup: /system.slice/cronie.service
			 └─410 /usr/bin/crond -n

1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 104. Maximum Depth of Binary Tree (49/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 88. Merge Sorted Array (50/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 70. Climbing Stairs (51/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 69. Sqrt(x) (52/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 67. Add Binary (53/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 53. Maximum Subarray (54/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 35. Search Insert Position (55/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 3. Longest Substring Without Repeating Characters (56/57))
1月 29 19:40:39 MA CROND[1654]: (abcdlsj) CMDOUT (processing: 1. Two Sum (57/57))
1月 29 19:40:39 MA CROND[1654]: pam_unix(crond:session): session closed for user abcdlsj
```
