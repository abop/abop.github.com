---
layout: post
title: "Debug with jekyll(rubygem)"
date: 2013-05-15 14:12
comments: true
categories: [jekyll, ruby]
---

折腾octopress的时候，遇到很多generate出错的情况，而提供的出错信息很难看出问题在哪，一般只能看到某个rb文件某行出了什么错，这个时候ruby的调试工具[debugger][]就可以派上用场了。

<h3> 1. 首先安装debugger gem </h3>
```bash
$ gem install debugger

# If install fails, try passing headers path
$ gem install debugger -- --with-ruby-include=PATH_TO_HEADERS
```

<h3> 2. 如果使用bundler，把gem添加到Gemfile </h3>

```
gem 'debugger'
```

<h3> 3. 在执行出错的rb文件中添加断点 </h3>

```ruby xxx.rb
#在出错的语句前添加
require 'debugger'; debugger
```

然后重新执行出错的rake task, 程序会停在断点处，直接输入变量名来查看变量，输入help查看命令列表。debugger用法详见[debugger tutorial][]。

 [debugger tutorial]: http://bashdb.sourceforge.net/ruby-debug.html
 [debugger]: https://github.com/cldwalker/debugger
 
