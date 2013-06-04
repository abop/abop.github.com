---
layout: post
title: "Basic linux shell commond (include vim)"
description: ""
categories: [linux, shell, vim]
---

## linux
* 执行命令后面加 &amp; ， 可以让任务后台运行
* jobs查看当前任务
* fg jobnumber切换某任务到前台
* 普通任务执行时ctrl+z挂起到后台，但任务会暂停
* bg jobnumber让挂起任务运行
* fg 切换到前台
* ps aux|grep 1370326845228.sh|tr -s ' ' | cut -d ' ' -f2 |xargs sudo kill
* tr -s '分隔符' 替换连续字符为一个
* cut -d '分隔符' -fn 分列，取第n列

====

## vim
* ctrl+L refresh screen buffer
* :split 分栏
* ctrl+ 方向键 切换分栏
* :e 打开文件
* :ls 查看当前打开的buffer（文件）
* :b num 切换文件（其中num为buffer list中的编号）
* :bn -- next buffer in the buffer list
* :bp -- previous buffer in the buffer list
* :b# -- previous buffer you was in
* gg=G格式化文件（"start=end"）
