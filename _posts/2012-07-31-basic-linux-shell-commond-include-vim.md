---
layout: post
title: "Basic linux shell commond (include vim)"
description: ""
category: Dev
tags: [shell, linux, vim]
---
{% include JB/setup %}

## linux
* 执行命令后面加 & ， 可以让任务后台运行
* jobs查看当前任务
* fg jobnumber切换某任务到前台
* 普通任务执行时ctrl+z挂起到后台，但任务会暂停
* bg jobnumber让挂起任务运行
* fg 切换到前台

====

## vim
* :split 分栏
* ctrl+ 方向键 切换分栏
* :e 打开文件
* :ls 查看当前打开的buffer（文件）
* :b num 切换文件（其中num为buffer list中的编号）
* :bn -- next buffer in the buffer list
* :bp -- previous buffer in the buffer list
* :b# -- previous buffer you was in
* gg=G格式化文件（"start=end"）
