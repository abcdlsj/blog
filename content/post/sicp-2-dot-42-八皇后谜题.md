+++
title = "SICP 2.42 八皇后谜题"
date = 2020-02-17T21:25:00+08:00
lastmod = 2020-04-23T10:34:33+08:00
tags = ["SICP"]
categories = ["learn"]
draft = false
author = "abcdlsj"
+++

> 去年一月就打算认真读下 SICP 的，但是当时读了第一章就放弃了，因为里面习题太多了！！!所以没坚持下去，今年 2020，刚好学校延迟开学，所以打算专心看一下，我给自己制定了七天 SICP 的任务（打算看完前三章），现在是第三天，第二章也看了一半了，习题真的多，我打算改变策略了，习题跳着做就好了。。。

<!--more-->

代码还算简单(简单我也看了半天。。。)，主要是督促一下自己好好看书(其实这道题个人觉得是 2.2.3 节最有价值的一道题了，很好诠释了 SICP 所要表达的 `抽象`)

```scheme
#lang sicp

(define nil (quote ()))

(define (filter predicate sequence)
  (cond ((null? sequence) nil)
		((predicate (car sequence))
		 (cons (car sequence)
			   (filter predicate (cdr sequence))))
		(else (filter predicate (cdr sequence)))))

(define (enumerate-interval low high)
  (if (> low high)
	  nil
	  (cons low (enumerate-interval (+ low 1) high))))

(define (accumulate op initial sequence)
  (if (null? sequence)
	  initial
	  (op (car sequence)
		  (accumulate op initial (cdr sequence)))))

(define (flatmap proc seq)
  (accumulate append nil (map proc seq)))

(define (queens board-size)
  (define (queen-cols k)
	(if (= k 0)
		(list empty-board)
		(filter
		 (lambda (positions) (safe? k positions))
		 (flatmap
		  (lambda (rest-of-queens)
			(map (lambda (new-row)
				   (adjoin-position new-row k rest-of-queens))
				 (enumerate-interval 1 board-size)))
		  (queen-cols (- k 1))))))
  (queen-cols board-size))

(define empty-board '())

(define (adjoin-position row col rest-of-queens)
  (cons row rest-of-queens))

(define (safe? column positions)
  (define (next-column-safe? new-row positions row-offset)
	(if (null? positions)
		#t
		(let ((this-row (car positions)))
		  (if (or (= this-row new-row)
				  (= (+ this-row row-offset) new-row)
				  (= (- this-row row-offset) new-row))
			  #f
			  (next-column-safe? new-row (cdr positions) (+ 1 row-offset))))))
  (next-column-safe? (car positions) (cdr positions) 1))
```
