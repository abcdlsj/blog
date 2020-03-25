+++
title = "安装TensorFlow"
date = 2018-11-10
lastmod = 2018-11-10T14:51:14+08:00
tags = ["Tensorflow"]
categories = ["learn"]
draft = false
+++

> 安装TensorFlow

<!--more-->
# 官网安装教程

[官网教程](https://www.tensorflow.org/install/pip#package-location)

不多说了,我主要想把自己遇到的问题记录一下

# 一些问题

## python版本

TensorFlow 最高支持3.6的Python版本,卸载原来版本官网上下载即可

## 安装conda

安装过程中,会出现很多问题,所以我推荐使用conda安装

[下载链接](https://www.anaconda.com/download/)


### 检查conda是否已安装并且在PATH中
```
conda -V
```
### 检查conda是否是最新的(不是必要的)
```
conda update conda
```
## 安装virtualenv

`virtualenv`是一个创建孤立的Python环境的工具

命令行中输入

```
pip install -U pip virtualenv
```
如果显示找不到pip或者pip不是最新的

可以执行以下代码更新pip
```
python -m pip install -U pip
```
## 创建一个虚拟环境
在终端客户端中输入以下内容，其中yourenvname是您要调用您的环境的名称，并将xx替换为您要使用的Python版本。（要首先查看可用的python版本列表，请键入`conda search "^python$"`并按Enter键。）
```
conda create -n yourenvname python=x.x anaconda
```
激活
```
conda activate yourenvname
```
下面的packageURL

[到这里选择替换](https://www.tensorflow.org/install/pip#package-location)
```
pip install --ignore-installed --upgrade packageURL
```

到这里还没完,继续下一步
## 安装TensorFlow
```
pip install --upgrade tensorflow
```
验证一下安装
```
python -c "import tensorflow as tf; tf.enable_eager_execution(); print(tf.reduce_sum(tf.random_normal([1000, 1000])))"
```
## 退出虚拟环境
```
conda deactivate
```
## 再次进入虚拟环境
```
source activate yourenvname
```
## 删除虚拟环境
```
conda remove -n yourenvname -all
```
