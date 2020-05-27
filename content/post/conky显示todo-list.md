+++
title = "conky 显示 todo-list"
date = 2019-12-13T23:28:00+08:00
lastmod = 2020-05-27T09:29:53+08:00
tags = ["conky", "org"]
categories = ["learn", "折腾"]
draft = false
author = "abcdlsj"
+++

> 作为一个 Emacser，使用 org-agenda 来管理日常是很正常的事情，于是想到在桌面显示自己的 todo-list，查阅资料之后发现很简单

<!--more-->


## conky 配置 {#conky-配置}

```shell
cat ~/.config/conky/org-todo.config
```

```text
conky.config = {
    alignment = 'top_left',
    background = true,
    cpu_avg_samples = 2,
    default_color = '336600',
    double_buffer = true,
    font = 'Sarasa Mono SC:size=14',
    gap_x = 10,
    gap_y = 50,
    minimum_width = 200,
    no_buffers = true,
    own_window = true,
    own_window_type = 'override',
    own_window_transparent = true,
    update_interval = 2.0,
    use_xft = true,
}

conky.text = [[
    ${execpi 300 /usr/bin/emacs --batch -l ~/.config/conky/org-todo.el -eval '(org-batch-agenda "t")'}
]];
```


## org-todo.el {#org-todo-dot-el}

```shell
cat ~/.config/conky/org-todo.el
```

```text
(require 'org)
(setq org-agenda-files (list "~/Dropbox/org/"))
(setq org-agenda-file-regexp "\\`[^.].*\\.org\\|.todo\\'")
(setq org-capture-templates
      '(
        ("t" "Task To Do!" entry
         (file+headline "~/Dropbox/org/task.org" "GTD")
         "* TODO %^{Task Name:}\n%u\n%a\n" :clock-in t :clock-resume t)
        ("r" "Book Reading Task" entry
         (file+headline "~/Dropbox/org/task.org" "Reading")
         "* TODO %^{Book Name:}\n%u\n%a\n" :clock-in t :clock-resume t)
        ("j" "Journal!!!" entry
         (file+olp+datetree "~/Dropbox/org/journal.org")
         "* %U - %^{heading} %^g\n %?\n" :tree-type week)
        ("n" "Notes!!!" entry
         (file+headline "~/Dropbox/org/notes.org" "NOTES")
         "* %U - %^{heading} %^g\n %?\n")
        ))
```
