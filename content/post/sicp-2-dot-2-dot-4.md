+++
title = "SICP 2.2.4 一个图形语言"
date = 2020-02-19T15:27:00+08:00
lastmod = 2020-03-25T20:57:00+08:00
tags = ["SICP"]
categories = ["learn"]
draft = false
author = "abcdlsj"
+++

> SICP 2.2.4 解决方案

<!--more-->

```text
在 2.2.4 中，通过对数据的组合来实现了复杂的图形，为了看到书中的例子，网上查找了解决方案，发现了两种比较好的
```

```racket
#lang racket

;;(#%require sicp-pict)
(require (planet "sicp.ss" ("soegaard" "sicp.plt" 2 1)))

(define (right-split painter n)
  (if (= n 0)
      painter
      (let ((smaller (right-split painter (- n 1))))
	(beside painter (below smaller smaller)))))

(paint (right-split einstein 5))
```

{{< figure src="/ox-hugo/fig2.png" caption="Figure 1: fig2" >}}

还可以

```racket
#lang sicp

(#%require sicp-pict)
;;(require (planet "sicp.ss" ("soegaard" "sicp.plt" 2 1)))

(define (right-split painter n)
  (if (= n 0)
      painter
      (let ((smaller (right-split painter (- n 1))))
	(beside painter (below smaller smaller)))))

(paint (right-split einstein 5))
```

{{< figure src="/ox-hugo/fig1.png" caption="Figure 2: fig1" >}}
