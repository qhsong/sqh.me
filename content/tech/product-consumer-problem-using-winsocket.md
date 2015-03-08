+++
Description = "燕山大学OS课程设计 生产者消费者问题 C/S"
Tags = ["生产者消费者问题","winsocket","课程设计"]
date = "2013-01-14T18:20:53+08:00"
title = "基于C/S下的多道程序缓冲区协调操作（生产者消费者问题）--燕山大学OS课设"

+++
<img class="alignnone size-full wp-image-522" alt="QQ截图20130114180724" src="http://sqh.me/blog/wp-content/uploads/2013/01/QQ截图20130114180724.jpg" width="706" height="147" />

&nbsp;

Os的课设，老师为了防止抄袭，增加了2个Buffer，我把这个作业改成了在WinSocket下的一个作业，如图2所示，

<img class="alignnone size-full wp-image-523 aligncenter" alt="QQ截图20130114180937" src="http://sqh.me/blog/wp-content/uploads/2013/01/QQ截图20130114180937.jpg" width="332" height="207" />

穆老板说，这个程序要好几台电脑演示就比较好看了。。

下载地址：<a href="http://sqh.me/blog/wp-content/uploads/2013/01/OS.zip">OS</a>

分为服务器端，生产者端和消费者端。

每个buffer最多15个元素

实验报告：<a href="http://sqh.me/blog/wp-content/uploads/2013/01/100104010001.pdf">100104010001.pdf</a>

已知bug：若消费者端得不到数据就会死机

改进方法，改为多线程，正在修改中
