+++
title = "Learn-elisp-in-sc"
date = 2019-11-15
lastmod = 2020-01-13T22:44:40+08:00
tags = ["elisp", "Emacs"]
categories = ["learn"]
draft = false
+++

> 这是learn x in minutes 的elisp教程，跟着做一遍

<!--more-->

```elisp
;;use C-x C-e(后面直接写Cxe)
;;基本运算符
(+ 3 (+ 1 3))
;;setq stores a value into a variable
(setq my-name "lsj")
;;C-x C-e 就会出现"lsj"在我的mini-buffer中
;;insert will insert "hello!" where the cursor is
(insert "hello!")
;;C-x C-e 光标处出现hello!
(insert "hello!" "world")
;;出现hello!world
;;也可以这样
(insert "hello!" "world " my-name)
;;hello!world lsj
;;也可以定义成一个functions
(defun hello () (insert "hello,my name is " my-name))
;;Cxe ->hello
(hello)
;;hello,my name islsj
;;hello()可以接受参数
(defun hello(name) (insert "hello " name))
(hello "lsj")
;;输入
(switch-to-buffer-other-window "*test*")
;;现在光标在*test*中
;;C-x o 切换回去
;;输入
(progn
  (switch-to-buffer-other-window "*test*")
  (hello "you" ))
;;progn 官方解释"progn is a special form that causes each of its arguments to be evaluated in sequence and then returns the value of the last one. The preceding expressions are evaluated only for the side effects they perform. The values produced by them are discarded."
;;翻译过来就是 progn 是一个特殊的过程，只返回最后一个表达式的值，前面表达式会被运行但是产生的值会被丢弃
;;所以我们看到*test*中出现hello you
;;抹除缓冲区
(progn
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (hello "there"))
;;也可以完成上面同时回到开始的buffer
(progn
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (hello "you ")
  ;;C-x o 等价于(other-window)
  (other-window 1))
;;定义局部变量 let
(let ((local-name "you"))
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (hello local-name)
  (other-window 1))
;;格式化字符串
(format "hello %s!\n" "visitor")
;;%s就跟c语言一样的，visitor替换%s
(defun hello (name)
  (insert (format "hello %s!\n" name)))
(hello "you")
;;hello you!
(defun greeting (name)
  (let ((your-name "Bastien"))
    (insert (format "hello %s!\n\nI am %s." name your-name))))
(greeting "you")
;;hello you!

;;I am Bastien.
;;一些交互
(read-from-minibuffer "Enter your name: ")
;;完善greeting()
(defun greeting (from-name)
  (let ((your-name (read-from-minibuffer "Enter your name:")))
    (insert (format "hello! I am %s and you are %s." from-name your-name))))
(greeting "Bastien")
;;minibuffer->Enter your name:lsj
;;hello! I am Bastien and you are lsj.
;;结合前面把内容展示到其他窗口
(defun greeting(from-name)
  (let ((your-name (read-from-minibuffer "Enter your name:")))
    (switch-to-buffer-other-window "*test*")
    (erase-buffer)
    (insert (format "hello ! i am %s and you are %s" from-name your-name))
    (other-window 1)))
(greeting "Bastien")
;;*test*->hello ! i am Bastien and you are lsj
;;创建一个列表
(setq list-of-names '("Saeah" "Chloe" "Mathilde"))
;;获取这个列表的第一个元素
(car list-of-names)
;;获取除了第一个元素之外的全部元素
(cdr list-of-names)
;;push
(push "Stephanie" list-of-names)
;;mapcar
(mapcar 'hello list-of-names)
;;
(defun greeting()
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (mapcar 'hello list-of-names)
  (other-window 1))
;;mapcar 调用hello 函数，依次使用"list-of-names"的元素作为参数
;;
(defun replace-hello-by-bonjour()
  (switch-to-buffer-other-window "*test*")
  (goto-char (point-min))
  (while (search-forward "hello")
    (replace-match "bonjour"))
    (other-window 1))
;;goto-char(point-min) 转到缓冲区的开头
;;(search-forward "hello") 搜索"hello"
;;elisp中 while x y 代表if x true,y;if x nil,out
(replace-hello-by-bonjour)
;;bonjour Stephanie!
;;bonjour Saeah!
;;bonjour Chloe!
;;bonjour Mathilde!
;;同时还会出现Search failed: Hello
;;可以把(search-forward)替换成(search-forward "Hello" nil t)
;;
(defun hello-to-bonjour()
  (switch-to-buffer-other-window "*test*")
  (erase-buffer)
  (mapcar 'hello list-of-names)
  (goto-char(point-min))
  (while(search-forward "hello" nil t)
    (replace-match "bonjour"))
  (other-window 1))
(hello-to-bonjour)
;;
(defun boldify-names ()
  (switch-to-buffer-other-window "*test*")
  (goto-char (point-min))
  (while (re-search-forward "bonjour \\(.+\\)!" nil t)
    (add-text-properties (match-beginning 1)
			   (match-end 1)
			   (list 'face 'bold)))
    (other-window 1))
(boldify-names)
;;re-search-forward 使用正则
;;add-text-properties 为文本添加属性
```
