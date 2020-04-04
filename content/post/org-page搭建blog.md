+++
title = "org-page 搭建 blog"
date = 2019-11-23
lastmod = 2020-04-04T19:03:55+08:00
tags = ["org-page", "org", "emacs"]
categories = ["blog"]
draft = false
+++

> 最近把 org-page 配置好了，大概今年五月份开始，就迷上了 emacs，期间爱上了配置 emacs，然后现在也开始使用 org-capture 做日常的笔记，我期间也写了挺多 org 笔记，所以才会想拿 org 来搭建 blog，搞了挺长时间，最开始的 EGO，到现在的 org-page，我终于弄好了，因为 org-page 太久没人修了，加上文档写的不全，对于小白不友好，所以我准备简单记录一下怎么搭建成功的

<!--more-->


## 下载 org-page {#下载-org-page}

[地址](https://github.com/sillykelvin/org-page)


## 配置 {#配置}

配置的话,我的是这样的

```elisp
(use-package! org-page
  :config
  (setq op/repository-directory "~/GithubPro/abcdlsj.github.io")
  (setq op/site-domain "https://abcdlsj.github.io")
  (setq op/personal-github-link "https://github.com/abcdlsj") ; if you want to show a personal github link
  (setq op/site-main-title "ABCDLSJ'S WORLD")
  (setq op/site-sub-title "=========>>享受专注")
  (setq op/personal-disqus-shortname "abcdlsj")
  (setq op/repository-org-branch "source")
  (setq op/repository-html-branch "master")
  (setq op/theme 'mdo))
```

我用的是 `doom-emacs` ，这是org-page 官方的示例

```elisp
;;; the following is only needed if you install org-page manually
(add-to-list 'load-path "path/to/org-page")
(require 'org-page)
(setq op/repository-directory "path/to/your/org/repository")
(setq op/site-domain "http://your.personal.site.com/")
;;; for commenting, you can choose either disqus, duoshuo or hashover
(setq op/personal-disqus-shortname "your_disqus_shortname")
(setq op/personal-duoshuo-shortname "your_duoshuo_shortname")
(setq op/hashover-comments t)
;;; the configuration below are optional
(setq op/personal-google-analytics-id "your_google_analytics_id")
```


## 使用 {#使用}


### 新建仓库 {#新建仓库}

```nil
M-x op/new-repository
```

输入路径之后，会初始化两个分支 ， `source` 和 `master` ，org-page 是在 `source` 下写文件，然后生成 html 文件到 `master` 分支，然后提交，就可以在 `yourname.github.io` 看到你的博客了。

对了，记得 `git remote` 你 github上面的仓库哦！


### 写博客 {#写博客}

```nil
M-x op/new-post
```

输入一系列配置之后，就可以了。这时候你会发现你的仓库地址分支会切换成 `source` ，这个分支就是给你写 org 文档的


### 提交 {#提交}

如果不太看得懂下面这步的话，可以先花二十分钟熟悉一下 git 操作，保证受益匪浅

```git
git add new blog (under source branch) and commit it ( in emacs, you can use magit, it would be much easier to do the process)
git push source(这步随便，因为博客本身只需要 html，也就是 master分支)
```

然后

```nil
M-x op/do-publication
```

选择 y n y y (或者我推荐 y n n n,然后你手动提交)就可以了，这个时候应该看不到博客因为 media 文件夹里面的东西是链接形式，你可以删除本地仓库中的 media 文件，然后复制源文件进去提交（我就是这样做的，所以每次 do-publication 都要操作一次，如果找到更好的办法我会写出来的）


## 参考链接 {#参考链接}

[^fn:1]

[^fn:1]: <https://dingmingxin.github.io/blog/2016/05/05/test-org-page/>
