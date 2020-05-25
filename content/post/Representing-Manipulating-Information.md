+++
title = "Representing-Manipulating-Information"
date = 2019-10-13
lastmod = 2020-02-24T22:36:59+08:00
tags = ["CSAPP", "OS"]
categories = ["learn"]
draft = false
+++

> CSAPP 第二章阅读笔记

<!--more-->


## learn {#learn}

> 没啥写的，有点忘了


## datalab {#datalab}

{{< figure src="/ox-hugo/lab1test.png" caption="Figure 1: datalab" >}}


### datalab 整数 {#datalab-整数}


#### bitXor {#bitxor}

我的思路是：先找出 x y 共同的 `1` ，然后找出共同的 `0`  ，然后 & 得到有相同字符的位置，最后取～ ，得到答案

```cpp
//1
/*
 * bitXor - x^y using only ~ and &
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return ~(x&y)&(~(~x&~y));
}
```


#### isTmax isTmin {#istmax-istmin}

`Tmin = 10000000 00000000 00000000 00000000`

```cpp
/*
 * tmin - return minimum two's complement integer
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  return 0x01<<31;
}
```


#### isTmax {#istmax}

Tmax = ~(Tmax+1)

Tmin = ~(Tmin+1)

```cpp
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
	return ! (x^(~(x+1))) & !!(~x);
}
```


#### allOddBits {#alloddbits}

x & 0xAAAAAAAA = 0xAAAAAAAA

构造0xAAAAAAAA

```cpp
int allOddBits(int x){
    int a = 0xaa|0xaa<<8;
    a = a|0xaa<<16;
    a = a|0xaa<<24;
    return !((a&x)^a);
}
```


#### negate {#negate}

负数的补码 = 反码 +1

```cpp
/*
 * negate - return -x
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return (~x)+1;
}
```


#### isAsciiDigit {#isasciidigit}

计算机加减一个数 = 加上那个数的反码+1

非负数右移31位==0

负数右移31位==1

```cpp
/*
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x){
	int left = x+~0x30+1;
	int right = 0x39+~x+1;
	return !(left>>31)&!(right>>31);
}
```


#### conditional {#conditional}

看到题目就知道要构造 `0` `1` ，要进行 `!` 运算又要最后返回 y z，于是想到根据 x 构造 0xffffffff

0x00000000^anynum = 0xffffffff&anynum;

```cpp
/*
 * conditional - same as x ? y : z
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
	/*
    *if x!=0,mask=0x00000000,so y&~mask==y and z&mask==0
    *if x==0,mask=0xffffffff,so y&~mask = y&0 =0; z&mask=z
    */
	int mask= ~!x+1;
    return (y & ~mask)|(z & mask);
}
```


#### isLessOrEqual {#islessorequal}

分两种情况讨论就行了

同号时 判断 y-x>=0 就行了

异号时 判断 x或者y 符号就行了

```cpp
/*
 * isLessOrEqual - if x <= y  then return 1, else return 0
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
	int signx = x>>31 & 0x01;
	int signy = y>>31 & 0x01;
	int isSameSign = !(signx^signy); // 相同符号为1
	return (isSameSign & !((y+~x+1)>>31))|((!isSameSign) & signx);
}
```


#### logicalNeg {#logicalneg}

`0` 和 `-0` 的 符号位都为 `0` 所以判断下 `(x|(~x+1))` 符号位就行了

```cpp
/*
 * logicalNeg - implement the ! operator, using all of
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4
 */
int logicalNeg(int x) {
	return ~((x|(~x+1))>>31)&0x01;
}
```


#### HowmanyBits {#howmanybits}

这道题开始完全没想到，后面参考小土刀的博客才做出来,代码不难，走一遍懂了

```cpp
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
	int tmp = x^(x>>31);
	int isZero = !tmp;
	int notZeroMark = (!(!tmp)<<31)>>31;
	int bit_16,bit_8,bit_4,bit_2,bit_1;
	bit_16 = !(!(tmp>>16))<<4;
	tmp = tmp >> bit_16;
	bit_8 = !(!(tmp>>8))<<3;
	tmp = tmp >> bit_8;
	bit_4 = !(!(tmp>>4))<<2;
	tmp = tmp >> bit_4;
	bit_2 = !(!(tmp>>2))<<1;
	tmp = tmp >>bit_2;
	bit_1 = !(!(tmp>>1));
	tmp = bit_1+bit_2+bit_4+bit_8+bit_16+2;
	return isZero|(tmp&notZeroMark);
}
```


### datalab 浮点 {#datalab-浮点}


#### floatScale2 {#floatscale2}

对于浮点数，首先得到其阶码
`int a = (uf>>23) & 0xff`

如果一个数是特殊值，其阶码为0xff，直接返回其值如果其阶码为0，代表是非规范数，则左移之后带上符号位返回即可如果是规范化数把阶码+1，然后带上符号位返回

```cpp
/*
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
	int a = (uf>>23) & 0xff;
	int flag = uf>>31<<31;
	int temp = uf;
	if(a == 0xff)
		return uf;
	else if (a == 0){
		temp = uf << 1;
		return temp|flag;
	} else {
		temp = temp + (0x01<<23);
		return temp|flag;
	}
	return temp;
}
```


#### floatFloat2Int {#floatfloat2int}

```cpp
/*
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  int a = ((uf >> 23) & 0xff) - 127;
	int flag = uf >> 31;
	if (a >= 32)
		return 0x80000000u;
	else if (a < 0)
		return 0;
	else {
		int temp = (uf | 0x800000) & 0xffffff;
		if (a >= 23) {
			int e = a - 23;
			temp = temp << e;
		} else {
			int e = 23 - a;
			temp = temp >> e;
		}
		return flag ? -temp : temp;
	}
}
```


#### floatPower2 {#floatpower2}

```cpp
/*
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 *
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatPower2(int x) {
	if (x > 127) {
		return 0x7f800000;
	} else if (x < -127) {
		return 0x0;
	} else {
		return (x + 127) << 23;
	}
}
```
