---
layout: post
title: How to Recover Files Deleted by Mistake in Git
category: Dev
tags: [git, recover, delete]
published: true
---

<ol>
<li>找到commit记录如：f5810e08df0cb7cf82608d65cdb709cb430df2ea</li>
<li>
执行以下命令即可恢复删除的文件
```bash
git checkout f5810e08df0cb7cf82608d65cdb709cb430df2ea path/to/file
```
</li>
<li>重新commit push</li>
</ol>
