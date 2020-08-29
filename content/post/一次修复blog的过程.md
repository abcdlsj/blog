+++
title = "一次修复 blog 的过程"
date = 2020-03-25T20:52:00+08:00
lastmod = 2020-06-13T22:20:57+08:00
tags = ["hugo", "blog"]
categories = ["折腾"]
draft = false
author = "abcdlsj"

+++

> 记一次修复 Blog 的过程

<!--more-->

> 博客前两天突然挂了，显示 deploy to github page failing
> 网上查了下，当时没解决，我还以为是 Hugo 的版本的问题（按理来说我没更新过应该不会出错的），我于是自己编译了一下 Hugo。发现没用。。。
> 于是我删了 Repository，新建，重新部署，还是不行
> 然后查到了这个

{{< figure src="/ox-hugo/githubPageBuildFaild.png" caption="Figure 1: githubPageBuildFaild" >}}

这也太坑了，原来 Github Pages 对 hugo 的支持不好，但是我不信邪，我的是静态文档，应该无所谓是什么生成的

于是我去查了下文档，找到了这个

{{< figure src="/ox-hugo/nojekyllImage.png" caption="Figure 2: Nojekyll" >}}

```shell
cd public && touch .nojekyll
```

就解决了！
