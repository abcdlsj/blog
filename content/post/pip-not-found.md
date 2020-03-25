+++
title = "pip not found"
date = 2019-01-21
lastmod = 2019-01-21T14:51:14+08:00
tags = ["zsh","pip","Python"]
categories = ["learn"]
draft = false
+++

> 我在linux环境下执行pip intsall xxx 时报错zsh: command not found:pip，当然这应该是切换shell的问题,其实最好解决办法就是不用on-my-zsh......

<!--more-->

# 初次尝试

> sudo apt-get install --reinstall python3-pip

查看是否安装成功

> pip3 -V

再执行

> pip3 install --upgrade pip

最后执行

> pip --version

# 出现问题:
发现还是

> zsh: command not found: pip

# 再次尝试
我再次执行了

> sudo apt-get install --reinstall python3-pip

然后想看一下pip3 -version

> pip3 -version

出现
```python
Traceback (most recent call last):
File "/usr/bin/pip3", line 9, in <module>
from pip import main
ImportError: cannot import name 'main'
```

解决：

> sudo gedit /usr/bin/pip3

把

```python
from pip import main
if __name__ == '__main__':
    sys.exit(main())
```
修改为
```python
from pip import __main__
if __name__ == '__main__':
    sys.exit(__main__._main())
```
执行

> pip3 install datetime

成功

# 其他方案

其实出现这类问题是因为没有配置好PATH(官方文档有写，网上也有)

有一个方案非常好(配置PATH)，大家可以参考下(需要梯子)

下面是链接

[bash: pip: command not found](https://stackoverflow.com/questions/42870537/zsh-command-cannot-found-pip)
