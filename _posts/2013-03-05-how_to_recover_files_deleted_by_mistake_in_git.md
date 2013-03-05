---
layout: post
title: how to recover files deleted by mistake in git
category: Dev
tags: [git, recover, delete]
published: true
---

1. 找到commit记录如：f5810e08df0cb7cf82608d65cdb709cb430df2ea
2. 执行以下命令即可恢复删除的文件
>     git checkout f5810e08df0cb7cf82608d65cdb709cb430df2ea path/to/file
3. 重新commit push