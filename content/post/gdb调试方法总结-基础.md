+++
title = "gdb 调试方法基础总结"
date = 2019-04-15
lastmod = 2019-04-16T14:51:14+08:00
tags = ["gdb"]
categories = ["learn"]
draft = false

toc = true

+++

> 原本早就准备系统学习 linux 知识的，但是懒惰的本性让我....这个原本一月就要学完的知识结果现在四月了还没学到多少....打算这段时间好好学习一下技术知识。

<!--more-->

 gdb 是 linux 系统中一个非常强大的调试工具，调试的基本思想是 “分析现象->假设错误原因->产生新的现象去验证假设”
这是 `<<Linux C 编程一站式学习>>` 中的一个 gdb 调试例子，非常的简明扼要，正好学习了，所以记录一下（这个例子在书上是给的错误代码，然后一步一步找到错误，但是运行环境不一样，错误不一定一样，所以我直接写正确的代码）
```c
#include <stdio.h>
int add_range(int low,int high)
{
	int sum=0;
	int i=0;
	for(i=low;i<=high;i++)
	{
		sum=i+sum;
	}
	return sum;
}
int main()
{
	int result[100];
	result[0]=add_range(1,10);
	result[1]=add_range(1,100);
	printf("result[0]=%d\nresult[1]=%d\n",result[0],result[1]);
	return 0;
}
```
我们需要在编译时加上`-g`选项，这样生成的可执行文件才能使用gdb调试
```
gcc -g test.c -o test
gdb test
```
`-g`选项是在可执行文件中加入源代码的信息，比如可执行文件的第几条机器指令对应源代码第几行，但并不是把整个源代码嵌入到可执行文件中，所以必须保证gdb能找到源文件
## 基础命令
### help
查看命令的类别
```shell
(gdb) help
List of classes of commands:

aliases -- Aliases of other commands
breakpoints -- Making program stop at certain points
data -- Examining data
files -- Specifying and examining files
internals -- Maintenance commands
obscure -- Obscure features
running -- Running the program
stack -- Examining the stack
status -- Status inquiries
support -- Support facilities
tracepoints -- Tracing of program execution without stopping the program
user-defined -- User-defined commands

Type "help" followed by a class name for a list of commands in that class.
Type "help all" for the list of all commands.
Type "help" followed by command name for full documentation.
Type "apropos word" to search for commands related to "word".
Command name abbreviations are allowed if unambiguous.
```
进一步某一类别有哪些命令，比如查看file下有那些命令
```
(gdb) help files
Specifying and examining files.

List of commands:

add-symbol-file -- Load symbols from FILE
add-symbol-file-from-memory -- Load the symbols out of memory from a dynamically loaded object file
cd -- Set working directory to DIR for debugger
core-file -- Use FILE as core dump for examining memory and registers
directory -- Add directory DIR to beginning of search path for source files
edit -- Edit specified file or function
exec-file -- Use FILE as program for getting contents of pure memory
file -- Use FILE as program to be debugged
forward-search -- Search for regular expression (see regex(3)) from last line listed
generate-core-file -- Save a core file with the current state of the debugged process
list -- List specified function or line
load -- Dynamically load FILE into the running program
nosharedlibrary -- Unload all shared object library symbols
path -- Add directory DIR(s) to beginning of search path for object files
pwd -- Print working directory
remote -- Manipulate files on the remote system
---Type <return> to continue, or q <return> to quit---
```
### list
list命令可以列出程序源代码
```shell
(gdb) list 1
1	#include <stdio.h>
2	int add_range(int low,int high)
3	{
4		int sum,i;
5		for(i=low;i<=high;i++)
6		{
7			sum=i+sum;
8		}
9		return sum;
10	}
```
一次只能读取十行
`gdb`有个好处是你回车默认是执行上次命令
list 可以简写为 l,也可以列出某个函数源代码
```
(gdb) l add_range
1	#include <stdio.h>
2	int add_range(int low,int high)
3	{
4		int sum,i;
5		for(i=low;i<=high;i++)
6		{
7			sum=i+sum;
8		}
9		return sum;
10	}
```
退出是`quit`
### start
开始执行程序是`start`
```
(gdb) start
Temporary breakpoint 1 at 0x6de: file test.c, line 12.
Starting program: /home/lsj/code/test

Temporary breakpoint 1, main () at test.c:12
12	{
```
看到gdb停在main函数变量定义以后的第一条语句处等待我们命令，gdb停下的这条语句即是下一条要执行的语句，`next`可以控制一条一条执行
```
(gdb) start
Temporary breakpoint 1 at 0x6ec: file test.c, line 13.
Starting program: /home/lsj/code/test

Temporary breakpoint 1, main () at test.c:13
13	{
(gdb) n
15		result[0]=add_range(1,10);
(gdb) n
16		result[1]=add_range(1,100);
(gdb) n
17		printf("result[0]=%d\nresult[1]=%d\n",result[0],result[1]);
(gdb) n
result[0]=55
result[1]=5050
18		return 0;
```
这样我们无法看到`add_range`函数里面
所以我们需要使用`step`
```
(gdb) start
Temporary breakpoint 1 at 0x6ec: file test.c, line 13.
Starting program: /home/lsj/code/test

Temporary breakpoint 1, main () at test.c:13
13	{
(gdb) step
15		result[0]=add_range(1,10);
(gdb)
add_range (low=1, high=10) at test.c:4
4		int sum=0;
```
可以看到停在了`add_range`函数变量定义之后的第一条语句处。在函数中有几种查看状态的方法，`backtrace`命令可以查看函数调用的浅栈。
```
(gdb) bt
#0  add_range (low=1, high=10) at test.c:4
#1  0x000055555555470a in main () at test.c:15
```
看到`add_range`函数是被`main`函数调用的,`main`函数浅栈编号为1，`add_range`函数浅栈编号为0.
可以用`info`命令来查看`add_range`函数局部变量的值
```
(gdb) i locals
sum = -138840185
i = 32767
```
要查看`main`函数的局部变量的值可以使用`frame`命令选择1号浅栈，然后`info`
```(gdb) f 1
#1  0x000055555555470a in main () at test.c:15
15		result[0]=add_range(1,10);
(gdb) i locals
result = {2, 32767, 0, 0, ..., 21845}
```
我们可以往下走几步，然后`print`出来
```
(gdb) step
15		result[0]=add_range(1,10);
(gdb)
add_range (low=1, high=10) at test.c:4
4		int sum=0;
(gdb)
5		int i=0;
(gdb)
6		for(i=low;i<=high;i++)
(gdb)
8			sum=i+sum;
(gdb)
6		for(i=low;i<=high;i++)
(gdb) p sum
$2 = 1
```
两次循环，所以sum值是1，前面的`$2`是编号
然后我们可以用`finish`命令让程序一直运行到当前函数返回为止
```
(gdb) finish
Run till exit from #0  add_range (low=1, high=10) at test.c:6
0x000055555555470a in main () at test.c:15
15		result[0]=add_range(1,10);
Value returned is $3 = 55
```
返回值是55,然后可以用`step`进行下一步，也就是给result赋值，然后`print`
```
(gdb) p result
$4 = {55, 32767, 0, 0, -8576,..., 21845}
```
看到55确实赋值给了result[0],下面进行第二次`add_range`调用
```
(gdb) s
16		result[1]=add_range(1,100);
(gdb)
add_range (low=1, high=100) at test.c:4
4		int sum=0;
(gdb) finish
Run till exit from #0  add_range (low=1, high=100) at test.c:4
0x000055555555471f in main () at test.c:16
16		result[1]=add_range(1,100);
Value returned is $2 = 5050
```
发现我们的答案已经出来，我们还可以用`set`,`print`命令修改变量的值
```
(gdb) set var result[2]=99
(gdb) p result
$3 = {55, 32767, 99, 0, -8576,..., 21845}
(gdb) s
17		printf("result[0]=%d\nresult[1]=%d\n",result[0],result[1]);
(gdb) p result
$4 = {55, 5050, 99, 0, -8576,..., 21845}
(gdb) print result[3]=50
$5 = 50
(gdb) p result
$6 = {55, 5050, 99, 50, -8576,..., 21845}
```
