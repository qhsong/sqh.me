+++
Description = "一些关于博客"
Tags = ["hugo","ngnix","LNMP"]
date = "2015-03-16T10:31:32+08:00"
title = "关于博客新系统的说明"

+++
从暑假到现在，拖拖踏踏的把这个博客系统架起来，起因是因为听了[内核恐慌：静态网站生成器](http://ipn.li/kernelpanic/3/)讲的，作为一个个人博客，Wordpress太大而且笨重，需要太多的资源。所以最后决定把这一套给抛弃了。另外一个个人的原因是因为自己开始喜欢上了Markdown这样的语言来撰写博客，Wordpress 的插件对Markdown支持并不是很好，所以就想找一个Markdown的编辑器。就这样使用上了Hugo作为我的博客生成。现在做笔记都是用Markdown语言，使用[马克飞象](http://www.snaildev.net/index.html)和evernote进行同步，还挺喜欢这个软件的，有喜欢的大家可以试试。  
![hugo](http://gohugo.io/)是一个用go语言做的静态网站生成器，生成速度快，由于使用的是go语言，所以有go语言一切的优点。我最喜欢的是没有依赖，整个安装程序就只有一个可执行文件，就再也没有别的东西了。  
在使用hugo时，最困难的就是进行新旧博客的转换。由于以前的网站没有采用静态URL，所以所有的文件都要自己重新复制粘贴，所以会比较累一些。好在花了几个晚上的时间把这个问题解决了。  
现在我采用的是Ngnix做反响代理，代理到hugo的端口，代码托管在github上，每次同步可以通过git进行同步。hugo的启动使用的命令是`hugo server -w -b http://sqh.me --appendPort=false`。这是一件非常有意思的事情。  
总之，换掉了冗杂的wordpress，拥抱go语言和静态网站。  
