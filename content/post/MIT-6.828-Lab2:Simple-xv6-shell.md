---
date: "2020-06-17T11:43:20+08:00"
title: "MIT 6.828 Lab 2: Simple xv6 shell"
authors: []
categories:
  - learn
tags:
  - 6.828
  - OS
  - xv6
draft: false
toc: true
typora-root-url: ../../static
---

>  Lab: Simple xv6 shell

> <!--希望两天后写完（不可能！明天考试，后天课设答辩，我都还没开始。。。）-->
>
> <!---\- 06-17 :timer_clock: 11:50-->
>
> <!--赶着补录完成了课设答辩（无力吐槽某专业了，考研率高，上研率低，就业质量差，专业设置真的合理？死堆没用的课程有用？学得越多越好（190 学分）？虽然可能大家心知肚明，但是呵呵）-->
>
> <!---\- -06-20 :timer_clock: 12:20-->
>
> <!--说实话，我竟然无从下手（不是完全不知道怎么做，而是不知道怎么做比较好，看来还是不太熟悉类 Linux 的底层），不知道是我对 xv6 不了解（准备对照 Linux 的底层实现来理解 xv6）还是对 c 语言不熟悉（翻了下 K&R 的 c 语言书，我 c 学的确实不好），翻了别人仓库，也没有看到一个很优雅简洁的实现-->
>
> <!--如果有更好的实现，我会来更新的，不能浪费时间了-->
>
> <!---\- 06-21 :timer_clock: 21:25-->

```cpp
#include "kernel/fcntl.h"
#include "kernel/param.h"
#include "kernel/types.h"
#include "user/user.h"

#define MAXSIZE 128

void redirect(int d, int pd[]) {
  close(d);
  dup(pd[d]);
  close(pd[0]);
  close(pd[1]);
}

char *splitpipe(char *cmd) {
  for (; *cmd && *cmd != '|'; cmd++)
    ;

  if (*cmd == '|') {
    *cmd++ = '\0';
    return cmd;
  }

  return 0;
}

int isspace(char c) { return c == ' ' || c == '\t' || c == '\n'; }

void execcmd(char *cmd) {
  char *argv[MAXARG];
  int argc = 0, inpos = 0, outpos = 0;

  for (; *cmd; *cmd++ = '\0') {
    if (!isspace(*cmd)) {
      argv[argc++] = cmd;

      if (*cmd == '<')
        inpos = argc;
      if (*cmd == '>')
        outpos = argc;

      for (; *cmd && !isspace(*cmd); cmd++)
        ;
      if (!*cmd) {
        break;
      }
    }
  }

  argv[argc] = 0;

  if (inpos) {
    argv[argc = inpos - 1] = 0;
    close(0);
    open(argv[inpos], O_RDONLY);
  }

  if (outpos) {
    argv[argc = outpos - 1] = 0;
    close(1);
    open(argv[outpos], O_CREATE | O_WRONLY);
  }

  if (argc == 0) {
    exit(0);
  }

  exec(argv[0], argv);

  fprintf(2, "argv: ");
  for (int i = 0; argv[i]; i++) {
    fprintf(2, " %s", argv[i]);
  }

  fprintf(2, "\n");
}

void parsecmd(char *cmd) {
  char *nextcmd = splitpipe(cmd);
  if (!nextcmd) {
    execcmd(cmd);
    fprintf(2, "exec: %s failed\n", cmd);
    exit(0);
  }

  int pd[2];
  pipe(pd);

  if (fork()) {
    redirect(1, pd);
    execcmd(cmd);
    fprintf(2, "exec: %s failed\n", cmd);
  } else if (fork()) {
    redirect(0, pd);
    parsecmd(nextcmd);
  }

  close(pd[0]);
  close(pd[1]);
  wait(0);
  wait(0);
  exit(0);
}

int getcmd(char *buf, int nbuf) {
  fprintf(2, "@ ");
  memset(buf, 0, nbuf);
  gets(buf, nbuf);
  if (buf[0] == 0)
    return -1;
  return 0;
}

int main(void) {
  static char buf[MAXSIZE];

  while (getcmd(buf, sizeof(buf)) == 0) {
    if (fork()) {
      wait(0);
    } else {
      parsecmd(buf);
      fprintf(2, "parsecmd not exit\n");
    }
  }

  exit(0);
}
```

