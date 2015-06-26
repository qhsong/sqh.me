+++
Description = ""generate Doxygen document into dash script index"
Tags = ["Doxygen","Dash","Golang"]
date = "2015-06-26T15:11:10+08:00"
title = "使用golang将Doxygen生成Dash Index"

+++

最近在研究Dash，一直在做DPDK，最近在看API文档的时候发现DPDK的API文档优化做的不太好（也有可能是我不会用），然后心血来潮的打算自己制作一个文本放到Dash中。

在制作Dash中有一个步骤是要生成Index文件，手动生成明显不现实，所有就用golang写了一个脚本来生成index文件。写了大概两天，还好。

地址是[https://github.com/qhsong/DoxygenToDash](https://github.com/qhsong/DoxygenToDash) 

在写的时候这个脚本考虑的是可以使用多线程，尝试了使用golang中的一些新特性，chan、go、select，感觉go性能不错。也有可能是我换了SSD的原因，SSD果然是提升体验的神器。

如果你觉得速度不够快，可以修改代码，多生成几个goroutine就行了。刚刚把DPDK的文档提交到github上，等作者审核。

最近计划更新博客的外观，一直没有很好的博客模板。学习一下Hugo，老感觉Hugo在生成预览的时候还是有点问题的，仔细研究一下。
