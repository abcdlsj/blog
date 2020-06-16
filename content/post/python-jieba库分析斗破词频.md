+++
title = "python jieba 库分析斗破词频"
date = 2018-07-19
lastmod = 2018-07-19T14:51:14+08:00
tags = ["Python"]
categories = ["learn"]
draft = false

+++

> 初学python时,老师介绍了使用jieba库来分析文本,我就想分析一下"世界名著"<<斗破苍穹>>,以下是代码.

<!--more-->

```python
    import jieba
    excludes = {"便是","有些","却是","能够","之中","他们","不过","有着","之上","一些"}#建立一个字典,去除不要的词,可以自己选择
    txt = open("C:\\Users\\70706\\Desktop\\23333\\python\\斗破苍穹.txt", "r", encoding='gbk').read()#这里编码使用gbk,而不是utf-8
    words  = jieba.lcut(txt)
    counts = {}
    for word in words:
    if len(word) == 1:  #排除单个字符的分词结果
        continue
    else:
        counts[word] = counts.get(word,0) + 1
    for word in excludes:
    del(counts[word])
    items = list(counts.items())
    items.sort(key=lambda x:x[1], reverse=True)s
    for i in range(15):
        word, count = items[i]
        print ("{0:<10}{1:>5}".format(word, count))


```

这是截图
![分析斗破去除后图][1]

[1]: https://res.cloudinary.com/dc15efw34/image/upload/v1531934521/16.png
