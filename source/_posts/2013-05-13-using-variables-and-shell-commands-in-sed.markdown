---
layout: post
title: "Using Variables and Shell-commands in Sed"
date: 2013-05-13 11:48
comments: true
categories: [linux, shell]
---
VIA:(http://blog.csdn.net/linwhwylb/article/details/7184748)

在使用sed对日志或者其它文本进行parse的过程当中，有时候我们需要引用外部变量的值，

或者获取一个shell命令执行的结果，以便达到更加可观的输出结果。这里介绍如何做到。

<!--more-->

## sed中使用变量替换

<ol><li>
sed命令使用双引号的情况下，使用$var直接引用

```bash
$ echo|sed "s/^/$RANDOM.rmvb_/g"
29328.rmvb_
# 上面例子引用了一个环境变量$RANDOM的值
```
</li>
<li>
sed命令使用单引号的情况下，使用'"$var"'引用

类似，我们可以看到

```bash
$ echo|sed 's/^/'"$RANDOM"'.rmvb_/g'
31338.rmvb_
```
</li></ol>

## sed中执行外部命令

<ol><li>
sed命令使用单引号的情况下使用'`shell command`'或者'$(shell command)'引用命令执行的结果

还是以上面案例分析，例子如下

```bash
$ echo|sed 's/^/'`echo $RANDOM`'.rmvb_/g'
8063.rmvb_
# 上面的例子使用了旧式的命令替换，也可以采用新式的命令替换方法，如下

$ echo|sed 's/^/'$(echo $RANDOM)'.rmvb_/g'
18554.rmvb_
# 下面例子取用当前日期作为结果的一部分，如下

$ echo|sed 's/^/'$(date +"%Y%m%d")'.rmvb_/g'
20120108.rmvb_
```
</li>

<li>sed命令使用双引号的情况下直接`shell command`或者$(shell command)引用命令执行的结果
类似的，双引号的情况，如下

```bash
$ echo|sed "s/^/$(date +"%Y%m%d").rmvb_/g"
20120108.rmvb_
# 使用环境变量$RANDOM以及旧式命令替换的例子

$ echo|sed "s/^/`echo $RANDOM`.rmvb_/g"
4988.rmvb_
```
</li></ol>

## 总结
在sed语句里面，变量替换或者执行shell命令，双引号比单引号少绕一些弯子
