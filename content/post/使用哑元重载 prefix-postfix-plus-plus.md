+++
title = "use dummy-parameter overload prefix/postfix ++"
date = 2020-04-04T18:57:00+08:00
lastmod = 2020-06-13T22:20:57+08:00
tags = ["CPP"]
categories = ["learn"]
draft = false
author = "abcdlsj"
toc = true
+++

> <!--最近因为选择找工作的原因，在准备春招实习面试的知识，好多东西不太会，之前看过的书有些都忘了（自闭了），希望我能够找到一个好的实习，前段时间粗略的复习了一下 C++，现在准备好好的复习一下 C++ 比较繁琐的方面，写博文记录一下我认为网上写的比较少的点。-->

<!--more-->

```text
哑元这个词还是开始是我查 void func(int); 这种只有参数类型的行参的作用的时候，看到别人说的。然后查查查找到了使用哑元来重载 ++
```

[isocpp: How can I overload the prefix and postfix forms of operators ++ and -\-?](https://isocpp.org/wiki/faq/operator-overloading#increment-pre-post-overloading)


## 正文 {#正文}

重载通过识别参数列表的不同来选择函数，对于 ++ 运算符，前置和后置是不同的，那么怎么识别的呢，就是通过 `dummy parameter`

前置自增是没有参数的，返回类型是引用（左值），后置自增是有哑元参数的，返回一个值（右值）。

```cpp
#include <bits/stdc++.h>
using namespace std;

class Number {
public:
  Number(int x) : val(x) {};
  Number& operator++ ();    // prefix ++
  Number  operator++ (int); // postfix ++
  void print() { cout << val; };
private:
  int val;
};

Number& Number::operator++ () {
  val += 1;
  return *this;
}

Number Number::operator++ (int) {
  Number ans = *this;
  ++(*this);  // or just call operator++()
  return ans; //返回未增的值
}

int main() {
  Number num1(1);
  cout << "num1 = ";
  num1.print();
  Number num2 = ++num1;
  cout << "\nnum2 = ++num1; num2 = ";
  num2.print();
  cout << "; num1 = ";
  num1.print();
  Number num3 = num1++;
  cout << "\nnum3 = num1++; num3 = ";
  num3.print();
  cout << "; num1 = ";
  num1.print();
}
```

下面是是输出

```cpp
num1 = 1
num2 = ++num1; num2 = 2; num1 = 2
num3 = num1++; num3 = 2; num1 = 3
```

网站上还写了另外一个版本的 postfix ++

```cpp
void Number::operator++ (int)
{
  ++(*this);  // or just call operator++()
}
```

但是这个版本不能返回 `this` 对象，也就是不能进行赋值。
