+++
title = "LeetCode 乙亥年最有趣的比赛题解"
date = 2020-01-21T01:07:00+08:00
lastmod = 2020-06-13T22:20:57+08:00
tags = ["LeetCode"]
categories = ["learn"]
draft = false
author = "abcdlsj"

toc = true

+++

> 比赛的时候做了一个小时一道都没做出来，1，5有思路但是也没做出来，事后找题解。。。

<!--more-->

[官方题解](https://leetcode-cn.com/circle/discuss/P05J24/)


## 1 {#1}

**题解**

```java
class Solution { public String q() { String s = "class Solution { public String q() { String s =  return s.substring(0,48)+(char)(1+'!')+s+(char)(1+'!')+';'+s.substring(48);}}"; return s.substring(0,48)+(char)(1+'!')+s+(char)(1+'!')+';'+s.substring(48);}}
```

这道题是检测你返回的代码和你提交的代码是否一样
[wiki Quine](https://en.wikipedia.org/wiki/Quine%5F(computing))

下面是 <a id="org7791e5d"></a> 中的 java 实现

```java
public class Quine { public static void main(String[] args) { char c=34; System.out.println(s+c+s+c+';'+'}'); } static String s="public class Quine { public static void main(String[] args) { char c=34; System.out.println(s+c+s+c+';'+'}'); } static String s=";}
```

```nil
public class Quine { public static void main(String[] args) { char c=34; System.out.println(s+c+s+c+';'+'}'); } static String s="public class Quine { public static void main(String[] args) { char c=34; System.out.println(s+c+s+c+';'+'}'); } static String s=";}
```

[python输出自身的四种写法](https://zhuanlan.zhihu.com/p/34882073)

lisp 版本

```scheme
((lambda (x)
   (list x (list (quote quote) x)))
 (quote
  (lambda (x)
	(list x (list (quote quote) x)))))
```


## 2 {#2}


## 3 {#3}

{{< figure src="/ox-hugo/leetcode_question.jpg" caption="Figure 1: 1" >}}

{{< figure src="/ox-hugo/leetcode_question2.jpg" caption="Figure 2: 2" >}}


### 1图是 Key {#1图是-key}

改后缀为 `txt` ，查看并取末尾字符串

```txt
ZmxhZyU3QkBMZWV0Q29kZXIlMkMlMjBIYXBweSUyME5ldyUyMFllYXIlMjAlMjhPM08lMjklMjAlMjElN0Q
```

```python
import base64
import urllib.parse
ans = base64.b64decode('ZmxhZyU3QkBMZWV0Q29kZXIlMkMlMjBIYXBweSUyME5ldyUyMFllYXIlMjAlMjhPM08lMjklMjAlMjElN0Q=')
ans_urlencoding = urllib.parse.unquote(ans.decode())
print(ans)
print(ans_urlencoding)
```

> ```text
> b'flag%7B@LeetCoder%2C%20Happy%20New%20Year%20%28O3O%29%20%21%7D'
> flag{@LeetCoder, Happy New Year (O3O) !}
> ```


### 2图是题目 {#2图是题目}

改后缀为 `rar` , 然后解压，但是我解压出错，只能看看别人的了，最后题目是

> 给一个长度为N的自然数序列 values ,  可以对这个序列做以下两种操作（每种操作次数不限）：
>
> 1.  交换序列中任意两个位置的 数字；
> 2.  选择序列中某一个数字 0 替换成任意的整数；
>
> 判断是否能通过一些操作，使得 values 中的元素是否满足 values[i+1]=values[i]+1  (0 <= i < N-1)。
>
> 1 <=  N <= 10^6
> 0 <= Values[I] <= 10^9


## 4 {#4}

````shell
objdump -j .rodata -S bomb
````

````nil
$ ./bomb
 _        _______  _______ _________ _______  _______  ______   _______
( \      (  ____ \(  ____ \\__   __/(  ____ \(  ___  )(  __  \ (  ____ \
| (      | (    \/| (    \/   ) (   | (    \/| (   ) || (  \  )| (    \/
| |      | (__    | (__       | |   | |      | |   | || |   ) || (__
| |      |  __)   |  __)      | |   | |      | |   | || |   | ||  __)
| |      | (      | (         | |   | |      | |   | || |   ) || (
| (____/\| (____/\| (____/\   | |   | (____/\| (___) || (__/  )| (____/\
(_______/(_______/(_______/   )_(   (_______/(_______)(______/ (_______/


Welcome to SyM's code safe!

Instructions:
	You need to solve this binary to get the key.
	The final key is a 12-word string.
	Once you've got it, make sure to submit it somewhere, since it should be quite valuable!

There are two phases. Here is the first one for warmup:
Please enter a password to get to the next stage (hint: the length of password is 8)
1eetC0de
Congratulations! You've passed the first stage.

===== Generating Key =====
 Key generation complete.
==========================

Here comes the second (and final) phase. It is a puzzle.
The puzzle input is 202020202020.
You need to find out the answer (a 64-bit integer as well) in order to get the key.

Surprisingly, I am kind enough to offer you some help.
You may enter any integer from 1 to 5, and I will calculate the answer to that input for you!
But for input like 202020202020, you need to do something special :)

If you've figured out the answer, simply enter -1 and then your answer to get the key!
Have fun decoding! (P.S. enter -2 to exit)

1
Try hard calculating...
Finished!!! The answer is:
0
````

<div class="table-caption">
  <span class="table-number">Table 1</span>:
  answer
</div>

| in | out |
|----|-----|
| 1  | 0   |
| 2  | 0   |
| 3  | 2   |
| 4  | 0   |
| 5  | 2   |


## 5 {#5}

{{< figure src="/ox-hugo/1000002.png" caption="Figure 3: 原二维码" >}}

反转颜色即可


## 6 {#6}
