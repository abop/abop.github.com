---
layout: post
title: 开发中心资源管理技术方案
category: work
published: true
---

需求背景
-----------
一些公用的脚本，不方便互相引用，浪费人力。考虑把开发中心的文档作为资源，用于文档间互相引用，或者被任务引用

交互设计
-----------
以文当中书写关键词的形式实现。格式如(rename重命名可选)：

1. 引用开发中心的文档
> --download[doc://docId rename]

2. 引用HDFS上的文件
> --download[hdfs://hdfsPath rename]

技术方案
-----------
1. 对于ShellJob和HiveJob在JobUtil createJob的时候解析脚本中的资源定义，添加到资源文件列表中
2. 把要运行job的脚本中的资源定义暂时删除（不提交数据库）
1. 在前置处理单元DownloadJob中处理文档中直接定义的资源下载
2. 对于开发中心的job，创建WithProcesserJob，添加DownloadJob到前置任务
3. 在开发中心 文档标题中 显示文档ID
4. 在开发中心提供上传资源到HDFS的功能，上传成功后直接将资源引用代码插入文档第一行
