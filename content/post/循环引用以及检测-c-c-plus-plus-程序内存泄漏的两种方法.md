+++
title = "std::shared_ptr 循环引用以及检测 C/C++ 内存泄漏"
date = 2020-05-14T16:17:00+08:00
lastmod = 2020-05-27T09:29:57+08:00
tags = ["CPP"]
categories = ["learn"]
draft = false
author = "abcdlsj"
+++

> std::shared\_ptr 循环引用
> Linux 检测 C/C++ 内存泄漏两种方法

<!--more-->

C++11 引入了智能指针的概念，使用引用计数，不用关心手动释放内存， 这些智能指针就包括 `std::shared_ptr/std::unique_ptr/std::weak_ptr` ，使用它们需要包含头文件 `<memory>` 。

> 注意：引用计数不是垃圾回收，引用计数能够尽快收回不再被使用的对象，同时在回收的过程中也不会造成长时间的等待， 更能够清晰明确的表明资源的生命周期。

<!--quoteend-->

> shared\_ptr 实现共享式拥有（shared ownership）概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用（reference）被销毁”时候释放。

但是这样会出现循环引用的问题，比如这样一个程序

```cpp
#include <iostream>
#include <memory>

struct A;
struct B;

struct A {
  std::shared_ptr<B> pointer;
  ~A() { std::cout << "A 被销毁" << std::endl; }
};

struct B {
  std::shared_ptr<A> pointer;
  ~B() { std::cout << "B 被销毁" << std::endl; }
};

int main() {
  auto a = std::make_shared<A>();
  auto b = std::make_shared<B>();
  std::cout << "a.use_count(): " << a.use_count() << "\tb.use_count(): "
			<< b.use_count() << std::endl;

  a->pointer = b;
  std::cout << "a.use_count(): " << a.use_count() << "\tb.use_count(): "
			<< b.use_count() << std::endl;

  b->pointer = a;
  std::cout << "a.use_count(): " << a.use_count() << "\tb.use_count(): "
			<< b.use_count() << std::endl;
}
```

输出为：

```text
: a.use_count(): 1	b.use_count(): 1
: a.use_count(): 1	b.use_count(): 2
: a.use_count(): 2	b.use_count(): 2
```

结果 A,B 都不会被销毁，因为 `a b` 内部同时引用了 `a b` ，这使得 `a b` 的引用计数变为 2，当离开作用域时， `a b` 智能指针被析构，各自的引用计数 -1，但是 `a b` 对象指向的内存区域引用计数不为零，而外部已经没有办法找到这块区域了，也就造成了内存泄露

{{< figure src="/ox-hugo/sharedPtrCR.png" caption="Figure 1: shared\_ptr" >}}

解决这个办法就是使用弱引用指针 `std::weak_ptr` ，它不控制所指向对象的生存期，指向一个 `std::shared_ptr` 管理的对象，但是它绑定到 `std::shared_ptr` 不会改变其引用计数。一旦最后一个指向对象的 `std::weak_ptr` 被销毁，对象就会被释放，即使 `std::weak_ptr` 指向对象，对象还是会被释放。

```cpp
#include <iostream>
#include <memory>

struct A;
struct B;

struct A {
  A() { std::cout << "A 被创建" << std::endl; }
  std::weak_ptr<B> pointer;
  ~A() { std::cout << "A 被销毁" << std::endl; }
};

struct B {
  B() { std::cout << "B 被创建" << std::endl; }
  std::weak_ptr<A> pointer;
  ~B() { std::cout << "B 被销毁" << std::endl; }
};

int main() {
  auto a = std::make_shared<A>();
  auto b = std::make_shared<B>();
  std::cout << "a.use_count(): " << a.use_count() << "\tb.use_count(): "
			<< b.use_count() << std::endl;
  a->pointer = b;
  std::cout << "a.use_count(): " << a.use_count() << "\tb.use_count(): "
			<< b.use_count() << std::endl;

  b->pointer = a;
  std::cout << "a.use_count(): " << a.use_count() << "\tb.use_count(): "
			<< b.use_count() << std::endl;
}
```

```text
: A 被创建
: B 被创建
: a.use_count(): 1	b.use_count(): 1
: a.use_count(): 1	b.use_count(): 1
: a.use_count(): 1	b.use_count(): 1
: B 被销毁
: A 被销毁
```

**这时 A B 都会被销毁了**

> 出现内存泄漏是非常麻烦的事情，这里介绍两个工具，这里用两个工具分析一下循环引用时发生的内存泄漏

****代码：****

```cpp
#include <iostream>
#include <memory>

struct A;
struct B;

struct A {
  std::shared_ptr<B> pointer;
  ~A() { std::cout << "A 被销毁" << std::endl; }
};

struct B {
  std::shared_ptr<A> pointer;
  ~B() { std::cout << "B 被销毁" << std::endl; }
};

int main() {
  auto a = std::make_shared<A>();
  auto b = std::make_shared<B>();
  a->pointer = b;
  b->pointer = a;
}
```

****工具：****


## valgrind {#valgrind}

```text
~/Dropbox/workspace/CPP/ptr $ valgrind --tool=memcheck --leak-check=full  ./shared_weak_ptr
==50675== Memcheck, a memory error detector
==50675== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==50675== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==50675== Command: ./shared_weak_ptr
==50675==
a.use_count()1	b.use_count()1
a.use_count()1	b.use_count()2
a.use_count()2	b.use_count()2
==50675==
==50675== HEAP SUMMARY:
==50675==     in use at exit: 64 bytes in 2 blocks
==50675==   total heap usage: 4 allocs, 2 frees, 73,792 bytes allocated
==50675==
==50675== 64 (32 direct, 32 indirect) bytes in 1 blocks are definitely lost in loss record 2 of 2
==50675==    at 0x4839DEF: operator new(unsigned long) (vg_replace_malloc.c:344)
==50675==    by 0x10A6D1: __gnu_cxx::new_allocator<std::_Sp_counted_ptr_inplace<A, std::allocator<A>, (__gnu_cxx::_Lock_policy)2> >::allocate(unsigned long, void const*) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x10A44C: std::allocator_traits<std::allocator<std::_Sp_counted_ptr_inplace<A, std::allocator<A>, (__gnu_cxx::_Lock_policy)2> > >::allocate(std::allocator<std::_Sp_counted_ptr_inplace<A, std::allocator<A>, (__gnu_cxx::_Lock_policy)2> >&, unsigned long) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x10A03D: std::__allocated_ptr<std::allocator<std::_Sp_counted_ptr_inplace<A, std::allocator<A>, (__gnu_cxx::_Lock_policy)2> > > std::__allocate_guarded<std::allocator<std::_Sp_counted_ptr_inplace<A, std::allocator<A>, (__gnu_cxx::_Lock_policy)2> > >(std::allocator<std::_Sp_counted_ptr_inplace<A, std::allocator<A>, (__gnu_cxx::_Lock_policy)2> >&) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x109DC5: std::__shared_count<(__gnu_cxx::_Lock_policy)2>::__shared_count<A, std::allocator<A>>(A*&, std::_Sp_alloc_shared_tag<std::allocator<A> >) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x109D0F: std::__shared_ptr<A, (__gnu_cxx::_Lock_policy)2>::__shared_ptr<std::allocator<A>>(std::_Sp_alloc_shared_tag<std::allocator<A> >) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x109C18: std::shared_ptr<A>::shared_ptr<std::allocator<A>>(std::_Sp_alloc_shared_tag<std::allocator<A> >) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x109AC1: std::shared_ptr<A> std::allocate_shared<A, std::allocator<A>>(std::allocator<A> const&) (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x10984A: std::shared_ptr<A> std::make_shared<A>() (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==    by 0x10922B: main (in /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr)
==50675==
==50675== LEAK SUMMARY:
==50675==    definitely lost: 32 bytes in 1 blocks
==50675==    indirectly lost: 32 bytes in 1 blocks
==50675==      possibly lost: 0 bytes in 0 blocks
==50675==    still reachable: 0 bytes in 0 blocks
==50675==         suppressed: 0 bytes in 0 blocks
==50675==
==50675== For lists of detected and suppressed errors, rerun with: -s
==50675== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```


## gcc asan {#gcc-asan}

```shell
g++ -fsanitize=address -fno-omit-frame-pointer -g -O2 shared_weak_ptr.cpp -o shared_weak_ptr
```

```text
~/Dropbox/workspace/CPP/ptr $ ./shared_weak_ptr
a.use_count()1	b.use_count()1
a.use_count()1	b.use_count()2
a.use_count()2	b.use_count()2

=================================================================
==50021==ERROR: LeakSanitizer: detected memory leaks

Indirect leak of 32 byte(s) in 1 object(s) allocated from:
    #0 0x7f615cbc6968 in operator new(unsigned long) /build/gcc/src/gcc/libsanitizer/asan/asan_new_delete.cc:104
    #1 0x56056bf885f1 in std::shared_ptr<B> std::make_shared<B>() /usr/include/c++/9.3.0/ext/new_allocator.h:114
    #2 0x56056bf885f1 in main /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr.cpp:51
    #3 0x7f615c5cc022 in __libc_start_main (/usr/lib/libc.so.6+0x27022)

Indirect leak of 32 byte(s) in 1 object(s) allocated from:
    #0 0x7f615cbc6968 in operator new(unsigned long) /build/gcc/src/gcc/libsanitizer/asan/asan_new_delete.cc:104
    #1 0x56056bf883c8 in std::__shared_count<(__gnu_cxx::_Lock_policy)2>::__shared_count<A, std::allocator<A>>(A*&, std::_Sp_alloc_shared_tag<std::allocator<A> >) /usr/include/c++/9.3.0/ext/new_allocator.h:114
    #2 0x56056bf883c8 in std::__shared_ptr<A, (__gnu_cxx::_Lock_policy)2>::__shared_ptr<std::allocator<A>>(std::_Sp_alloc_shared_tag<std::allocator<A> >) /usr/include/c++/9.3.0/bits/shared_ptr_base.h:1344
    #3 0x56056bf883c8 in std::shared_ptr<A>::shared_ptr<std::allocator<A>>(std::_Sp_alloc_shared_tag<std::allocator<A> >) /usr/include/c++/9.3.0/bits/shared_ptr.h:359
    #4 0x56056bf883c8 in std::shared_ptr<A> std::allocate_shared<A, std::allocator<A>>(std::allocator<A> const&) /usr/include/c++/9.3.0/bits/shared_ptr.h:702
    #5 0x56056bf883c8 in std::shared_ptr<A> std::make_shared<A>() /usr/include/c++/9.3.0/bits/shared_ptr.h:718
    #6 0x56056bf883c8 in main /home/abcdlsj/Dropbox/workspace/CPP/ptr/shared_weak_ptr.cpp:50
    #7 0x7f615c5cc022 in __libc_start_main (/usr/lib/libc.so.6+0x27022)

SUMMARY: AddressSanitizer: 64 byte(s) leaked in 2 allocation(s).
```

参考链接：

<https://changkun.de/modern-cpp/zh-cn/05-pointers/index.html#%E6%80%BB%E7%BB%93>

<https://www.ibm.com/developerworks/cn/linux/l-cn-valgrind/index.html>

<https://www.cnblogs.com/justin-y-lin/p/11314059.html>
