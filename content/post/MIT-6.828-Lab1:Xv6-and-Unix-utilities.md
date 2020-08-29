---
date: "2020-06-16T10:43:35+08:00"
title: "MIT 6.828 Lab 1: Xv6 and Unix utilities"
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

> Lab: Xv6 and Unix utilities

> <!--最近小半个月，很多事情都不想做，于是索性全部时间看动漫去了（花了半个月把一直想看的火影看得差不多了）。一方面，我要秋招，很多知识点掌握得不够牢固，牢固了也不一定说得出来；另一方面，学校事情非常多（我认为很多，一堆课设、实验、作业，偶尔还要交些垃圾东西，我都准备课设找代写，可惜，还没找到，学校要求还是挺高的，单片机课设竟然要求 20% 汇编。。。），根本不想去看书，写代码。但是看到很多人找到很好的实习，秋招也很快开始了，然后又觉得自己好像从放假回来没有太大的长进（感觉我就像在原地转圈一样）。所以，我想要从 `圈` 中出来，放弃不切实际的想法，多看书，多总结，多写 lab。我现在对自己非常清楚，只要没东西限制我，我真的没有自制力！-->
>
> <!---\- 06-16-->
>
> 基本完成实验了，但是一些细节自己还是没处理好，有的地方也不是特别清晰（对，就是 `primes`），明天应该会再看下别人的实现，然后多完善一下 lab1 
>
> -\- 06-17 :timer_clock: 01:30
>
> 又看了会并行素数筛，go 版本很好理解，至于实验，嗯，我是 fw。。。
>
> 继续看，继续学（今天原本要停电，都做好看一天动漫的准备了。。。然后早上起床发现不停了。。。）
>
> -\- 06-17 :timer_clock: 11:30

## xv6 System Call

| system call               | description                                                  |
| :------------------------ | :----------------------------------------------------------- |
| fork()                    | Create a process                                             |
| exit(xstatus)             | Terminate the current process with xstatus indicating success or failure |
| wait(*xstatus)            | Wait for a child process to exit and copy the child’s exit status to xstatus |
| kill(pid)                 | Terminate process pid                                        |
| getpid()                  | Return the current process’s pid                             |
| sleep(n)                  | Sleep for n clock ticks                                      |
| exec(filename, *argv)     | Load an executable file and execute it                       |
| sbrk(n)                   | Grow process’s memory by n bytes                             |
| open(filename, flags)     | Open a file; the flags indicate read/write                   |
| read(fd, buf, n)          | Read n bytes from an open file into buf                      |
| write(fd, buf, n)         | Write n bytes to an open file                                |
| close(fd)                 | Release open file fd                                         |
| dup(fd)                   | Duplicate fd                                                 |
| pipe(p)                   | Create a pipe and return fd’s in p                           |
| chdir(dirname)            | Change the current directory                                 |
| mkdir(dirname)            | Create a new directory                                       |
| mknod(name, major, minor) | Create a device file                                         |
| fstat(fd)                 | Return info about an open file                               |
| link(f1, f2)              | Create another name (f2) for the file f1                     |
| unlink(filename)          | Remove a file                                                |

## sleep

Implement the UNIX program sleep for xv6; your sleep should pause for a user-specified number of ticks. (A tick is a notion of time defined by the xv6 kernel, namely the time between two interrupts from the timer chip.) Your solution should be in the file `user/sleep.c`.

- Look at some of the other programs in `user/` to see how you can obtain the command-line arguments passed to a program. If the user forgets to pass an argument, sleep should print an error message.
- The command-line argument is passed as a string; you can convert it to an integer using `atoi` (see user/ulib.c).
- Use the system call `sleep` (see user/usys.S and kernel/sysproc.c).
- Make sure `main` calls `exit()` in order to exit your program.
- Add the program to `UPROGS` in Makefile and compile user programs by typing `make fs.img`.
- Look at Kernighan and Ritchie's book *The C programming language (second edition)* (K&R) to learn about C.

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int main (int argc, char *argv[]) {
  if (argc != 2) {
    write(2, "Error Message\n", strlen("Error Message\n"));
  }
  int ticks = atoi(argv[1]);
  sleep(ticks);
  exit();
}
```

添加 `	$U/_sleep\` 到 `UPROGS` ，然后 `make fs.img` ，`make qemu` 运行

## pingpong

Write a program that uses UNIX system calls to `ping-pong` a byte between two processes over a pair of pipes, one for each direction. The parent sends by writing a byte to `parent_fd[1]` and the child receives it by reading from `parent_fd[0]`. After receiving a byte from parent, the child responds with its own byte by writing to `child_fd[1]`, which the parent then reads. Your solution should be in the file `user/pingpong.c`.

- Use `pipe` to create a pipe.
- Use `fork` to create a child.
- Use `read` to read from the pipe, and `write` to write to the pipe.

>A pipe is a small kernel buffer exposed to processes as a pair of file descriptors, one for reading
>and one for writing. Writing data to one end of the pipe makes that data available for reading from
>the other end of the pipe. Pipes provide a way for processes to communicate.
>
>-\-  :books: 1.3 Pipes
>
>文件描述符对，从一个写入，另一个可以读取

```cpp
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int main (int argc, char *argv[]) {
  char buf[64];
  int parent_fd[2], child_fd[2];
  pipe(parent_fd);
  pipe(child_fd);

  if (fork()) {
    write(parent_fd[1], "ping", strlen("ping"));
    read(child_fd[0], buf, 4);
    printf("%d: received %s\n", getpid(), buf);
  } else {
    read(parent_fd[0], buf, 4);
    printf("%d: received %s\n", getpid(), buf);
    write(child_fd[1], "pong", strlen("pong"));
  }

  exit();
}
```

结果：

```
$ pingpong 
4: received ping
3: received pong
```

## primes

Write a concurrent version of prime sieve using pipes. This idea is due to Doug McIlroy, inventor of Unix pipes. The picture halfway down [this page](http://swtch.com/~rsc/thread/) and the surrounding text explain how to do it. Your solution should be in the file `user/primes.c`.

Your goal is to use `pipe` and `fork` to set up the pipeline. The first process feeds the numbers 2 through 35 into the pipeline. For each prime number, you will arrange to create one process that reads from its left neighbor over a pipe and writes to its right neighbor over another pipe. Since xv6 has limited number of file descriptors and processes, the first process can stop at 35.

Some hints:

- Be careful to close file descriptors that a process doesn't need, because otherwise your program will run xv6 out of resources before the first process reaches 35.
- Once the first process reaches 35, you should arrange that the pipeline terminates cleanly, including all children (Hint: read will return an end-of-file when the write-side of the pipe is closed).
- It's simplest to directly write 32-bit `int`s to the pipes, rather than using formatted ASCII I/O.
- You should create the processes in the pipeline as they are needed.

> 文档地址：
>
> https://swtch.com/~rsc/thread/

伪代码：

```
p = get a number from left neighbor
print p
loop:
    n = get a number from left neighbor
    if (p does not divide n)
        send n to right neighbor
```



> go 版本的并行素数筛，比较好理解
>
> 然后还有理解图：
>
> https://divan.dev/demos/primesieve/

```go
// A concurrent prime sieve

package main

import "fmt"

// Send the sequence 2, 3, 4, ... to channel 'ch'.
func Generate(ch chan<- int) {
	for i := 2; ; i++ {
		ch <- i // Send 'i' to channel 'ch'.
	}
}

// Copy the values from channel 'in' to channel 'out',
// removing those divisible by 'prime'.
func Filter(in <-chan int, out chan<- int, prime int) {
	for {
		i := <-in // Receive value from 'in'.
		if i%prime != 0 {
			out <- i // Send 'i' to 'out'.
		}
	}
}

// The prime sieve: Daisy-chain Filter processes.
func main() {
	ch := make(chan int) // Create a new channel.
	go Generate(ch)      // Launch Generate goroutine.
	for i := 0; i < 10; i++ {
		prime := <-ch
		fmt.Println(prime)
		ch1 := make(chan int)
		go Filter(ch, ch1, prime)
		ch = ch1
	}
}
```

> 代码实现（这是看别人的。。。自己写我感觉写不出来。。。）
>
> -\- 06-17
>
> -------
>
> 原来别人也是抄论文的写法（我还以为我书白读了，怎么想不到 redirect）
>
> 论文原文：
>
> https://www.cs.dartmouth.edu/~doug/sieve/sieve.pdf
>
> -\- 06-19 添加

```c
#include "kernel/stat.h"
#include "kernel/types.h"
#include "user/user.h"

void cull(int p) {
  int n;

  while (read(0, &n, sizeof(n))) {
    // n is not prime
    if (n % p != 0) {
      write(1, &n, sizeof(n));
    }
  }
}

void redirect(int k, int pd[]) {
  close(k);
  dup(pd[k]);
  close(pd[0]);
  close(pd[1]);
}

void right() {
  int pd[2], p;

  // read p
  if (read(0, &p, sizeof(p))) {
    printf("prime %d\n", p);

    pipe(pd);

    if (fork()) {
      // parent
      redirect(0, pd);
      right();
    } else {
      redirect(1, pd);
      cull(p);
    }
  }
}

int main(int argc, char *argv[]) {
  int pd[2];
  pipe(pd);

  if (fork()) {
    redirect(0, pd);
    right();
  } else {
    redirect(1, pd);
    for (int i = 2; i < 36; i++) {
      write(1, &i, sizeof(i));
    }
  }

  exit();
}

```

## find

Write a simple version of the UNIX find program: find all the files in a directory tree whose name matches a string. Your solution should be in the file `user/find.c`.

- Look at user/ls.c to see how to read directories.
- Use recursion to allow find to descend into sub-directories.
- Don't recurse into "." and "..".
- Changes to the file system persist across runs of qemu; to get a clean file system run make clean and then make qemu.
- You'll need to use C strings. Have a look at K&R (the C book), for example Section 5.5.

Optional: support regular expressions in name matching. `grep.c` has some primitive support for regular expressions.

> 支持 `re` 需要复制 `user/grep.c` 中的 `matchhere matchstar match` 
>
> 可以看下 `user/ls.c` ，只需要修改为递归实现就可以了

```c
#include "kernel/types.h"
#include "kernel/fs.h"
#include "kernel/stat.h"
#include "user/user.h"

// ------ form user/ls.c ------
char *fmtname(char *path) {
  static char buf[DIRSIZ + 1];
  char *p;

  for (p = path + strlen(path); p >= path && *p != '/'; p--)
    ;
  p++;

  if (strlen(p) >= DIRSIZ)
    return p;

  memmove(buf, p, strlen(p));
  memset(buf + strlen(p), ' ', DIRSIZ - strlen(p));
  return buf;
}

// ------ form user/grep.c ------

// Regexp matcher from Kernighan & Pike,
// The Practice of Programming, Chapter 9.

int matchhere(char *, char *);
int matchstar(int, char *, char *);

int match(char *re, char *text) {
  if (re[0] == '^')
    return matchhere(re + 1, text);
  do { // must look at empty string
    if (matchhere(re, text))
      return 1;
  } while (*text++ != '\0');
  return 0;
}

// matchhere: search for re at beginning of text
int matchhere(char *re, char *text) {
  if (re[0] == '\0')
    return 1;
  if (re[1] == '*')
    return matchstar(re[0], re + 2, text);
  if (re[0] == '$' && re[1] == '\0')
    return *text == '\0';
  if (*text != '\0' && (re[0] == '.' || re[0] == *text))
    return matchhere(re + 1, text + 1);
  return 0;
}

// matchstar: search for c*re at beginning of text
int matchstar(int c, char *re, char *text) {
  do { // a * matches zero or more instances
    if (matchhere(re, text))
      return 1;
  } while (*text != '\0' && (*text++ == c || c == '.'));
  return 0;
}

// ----- find implementation -----

void find(char *path, char *re) {
  char buf[512], *p;
  int fd;
  struct dirent de;
  struct stat st;

  if ((fd = open(path, 0)) < 0) {
    fprintf(2, "find: cannot open %s\n", path);
    return;
  }

  if (fstat(fd, &st) < 0) {
    fprintf(2, "find: cannot open %s\n", path);
    close(fd);
    return;
  }
  switch (st.type) {
  case T_FILE:
    if (match(re, fmtname(path)))
      printf("%s\n", path);
    break;

  case T_DIR:
    if (strlen(path) + 1 + DIRSIZ + 1 > sizeof buf) {
      printf("find: path too lang\n");
      break;
    }

    strcpy(buf, path);
    p = buf + strlen(buf);
    *p++ = '/';
    while (read(fd, &de, sizeof(de)) == sizeof(de)) {
      if (de.inum == 0)
        continue;
      memmove(p, de.name, DIRSIZ);
      p[DIRSIZ] = 0;
      if (stat(buf, &st) < 0) {
        printf("find: cannot stat %s\n", buf);
        continue;
      }

      if (!strcmp(de.name, ".") || !strcmp(de.name, ".."))
        continue;

      // 递归实现
      find(buf, re);
    }
    break;
  }

  close(fd);
}

int main(int argc, char *argv[]) {
  if (argc <= 2) {
    fprintf(2, "usage: find <path> <expression>\n");
  }

  find(argv[1], argv[2]);
  exit();
}
```

## xargs

Write a simple version of the UNIX xargs program: read lines from standard input and run a command for each line, supplying the line as arguments to the command. Your solution should be in the file `user/xargs.c`.

- Use `fork` and `exec` system call to invoke the command on each line of input. Use `wait` in the parent to wait for the child to complete running the command.
- Read from stdin a character at the time until the newline character ('\n').
- kernel/param.h declares MAXARG, which may be useful if you need to declare an argv.
- Changes to the file system persist across runs of qemu; to get a clean file system run make clean and then make qemu.

> 用标准输入代替命令的参数

```c
#include "kernel/types.h"
#include "kernel/param.h" // MAXARG
#include "kernel/stat.h"
#include "user/user.h"

int main(int argc, char *argv[]) {
  int argv_len;
  char buf[128] = {0};
  char *max_args[MAXARG];

  for (int i = 1; i < argc; i++) {
    max_args[i - 1] = argv[i];
  }

  while (gets(buf, sizeof(buf))) {
    int buf_len = strlen(buf);
    if (buf_len < 1)
      break;
    buf[buf_len - 1] = 0;

    argv_len = argc - 1;
    char *p = buf;

    while (*p) {
      while (*p && (*p == ' '))
        *p++ = 0;
      if (*p)
        max_args[argv_len++] = p;
      while (*p && (*p != ' '))
        p++;
    }

    if (argv_len >= MAXARG) {
      printf("Too many args\n");
      exit();
    }

    if (argv_len < 1) {
      printf("Too few args\n");
      exit();
    }

    max_args[argv_len] = 0;

    if (fork()) {
      wait();
    } else {
      exec(max_args[0], max_args);
      exit();
    }
  }
  exit();
}
```

## End

> 多去尝试，体会不一样的东西！