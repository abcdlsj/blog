---
date: "2021-04-01T19:42:49+08:00"
title: "CS144 Lab Notes"
tags:
  - CS144
  - Network
draft: true
typora-root-url: ../../static
---

> 电脑环境

```text
                   -`                    abcdlsj@Lenovo-13ARE
                  .o+`                   --------------------
                 `ooo/                   OS: Arch Linux x86_64
                `+oooo:                  Host: 82DM Lenovo XiaoXinPro-13ARE 2020
               `+oooooo:                 Kernel: 5.11.4-zen1-1-zen
               -+oooooo+:                Uptime: 1 hour, 54 mins
             `/:-:++oooo+:               Packages: 1326 (pacman)
            `/++++/+++++++:              Shell: zsh 5.8
           `/++++++++++++++:             Resolution: 2560x1600
          `/+++ooooooooooooo/`           WM: i3
         ./ooosssso++osssssso+`          Theme: Adwaita-dark [GTK2/3]
        .oossssso-````/ossssss+`         Icons: Papirus-Dark [GTK2/3]
       -osssssso.      :ssssssso.        Terminal: alacritty
      :osssssss/        osssso+++.       CPU: AMD Ryzen 7 4800U with Radeon Graphics (16) @ 1.800GHz
     /ossssssss/        +ssssooo/-       GPU: AMD ATI 03:00.0 Renoir
   `/ossssso+/:-        -:/+osssso+-     Memory: 5086MiB / 13912MiB
  `+sso+:-`                 `.-/+oso:
 `++:.                           `-/+/
 .`                                 `/
```

## Lab 0: networking warmup

https://cs144.github.io/assignments/lab0.pdf

> lab 0 比较简单，环境设置遇到  `out_of_range() not found` 的一个问题，然后添加 `#include <stdexcept>` 就可以了

Lab 0 实现有两个任务，`webget` 和 `byte steam`

`webget` 实现如下：

```cpp
TCPSocket sock{};
sock.connect(Address(host, "http"));
string input("GET " + path + " HTTP/1.1\r\nhost: " + host + "\r\n\r\n");
sock.write(input);
sock.shutdown(SHUT_WR);
while (!sock.eof()) {
    cout << sock.read();
}
sock.close();
```

`byte stream` 则是实现一个类似双端队列的东西，我直接使用 `std::deque<char>` 实现。

这里写下 `eof` 实现：

```cpp
bool ByteStream::eof() const { return input_ended() && buffer_empty(); }
```

`eof` 代表无法继续从 `stream` 读出数据，不仅要判断输入完成，还要判断 `buffer` 已被读完。

## Lab 1: stitching substrings into a byte stream

![cs144impl_dataflow_modules](img/cs144impl_dataflow_modules.png)

