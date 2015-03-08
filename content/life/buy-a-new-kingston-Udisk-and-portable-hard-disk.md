+++
Description = "新买了一些东西"
Tags = ["Kingston","U盘","移动硬盘"]
date = "2011-06-01T17:35:05+08:00"
ds_title = "新购入Kingston DT101 G2 8G U盘和一个自组硬盘"
title = "新购入Kingston DT101 G2 8G U盘和一个自组硬盘"

+++
最近新购入一个U盘，跑去北京买了个移动硬盘盒，支持eSata接口的，仅仅硬盘盒就135大洋啊，心疼啊。USB接口的就70多，自己还真是脑子进水了，这边流行是脑子被驴踢了。。。。。。。
<a href="http://www.newegg.com.cn/Product/22-c13-028.htm">WD 西部数据 Scorpio 2.5 英寸500GB SATA 3.0Gb/s 笔记本硬盘 WD5000BPVT</a>

买过来之后，组装起来才发现，悲剧了，eSata接口要用外接电源才能使用。。。以后用eSata口要使用外置电源了。或者带着USB线做电源了。

硬盘到后最大的问题是发现硬盘的工作电流是 0.55A,标准呢USB2.0接口设计输出电流是0.5A，这让我有点头疼，这样的话岂不是硬盘要在坏道下工作。事实证明没问题，我的小Y还能轻松带动。。

闲话少说，直接上图

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/20110528031.jpg"><img class="alignnone size-large wp-image-204" title="20110528031" src="http://sqh.me/blog/wp-content/uploads/2011/06/20110528031-768x1024.jpg" alt="" width="768" height="1024" /></a>

硬盘盒，内部拆开了

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/20110528030.jpg"><img class="alignnone size-large wp-image-203" title="20110528030" src="http://sqh.me/blog/wp-content/uploads/2011/06/20110528030-1024x768.jpg" alt="" width="1024" height="768" /></a>

WD500GB硬盘。。。

下面是测试数据了：

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/USB-1-write.png"><img class="alignnone size-full wp-image-198" title="USB-1-write" src="http://sqh.me/blog/wp-content/uploads/2011/06/USB-1-write.png" alt="" width="594" height="486" /></a>

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/USB-1.png"><img class="alignnone size-full wp-image-197" title="USB-1" src="http://sqh.me/blog/wp-content/uploads/2011/06/USB-1.png" alt="" width="594" height="486" /></a>

读取和写入速度都维持在25M左右，差不到达USB接口的最高速度了。略有不爽的是突发存取时间有点长……哎，毕竟我不用他进行突发存取嘛。。

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/USB-2-wirite.png"><img class="alignnone size-full wp-image-200" title="USB-2-wirite" src="http://sqh.me/blog/wp-content/uploads/2011/06/USB-2-wirite.png" alt="" width="594" height="486" /></a>

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/USB-2-read.png"><img class="alignnone size-full wp-image-199" title="USB-2-read" src="http://sqh.me/blog/wp-content/uploads/2011/06/USB-2-read.png" alt="" width="594" height="486" /></a>

前面哪个测试是使用的一个USB接口，这个我使用的是两个USB接口，速度差不多，看来这个电流的问题没必要担心了。

下面是esata接口的测试。

为排除供电原因，我先使用的是USB供电测试：

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-Usb-elec-write.png"><img class="alignnone size-full wp-image-193" title="eSata-Usb elec-write" src="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-Usb-elec-write.png" alt="" width="594" height="486" /></a>

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-Usb-elec-read.png"><img class="alignnone size-full wp-image-192" title="eSata-Usb elec-read" src="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-Usb-elec-read.png" alt="" width="594" height="486" /></a>

看到这张图，我懵了，这个eSata的速度也太不和谐了吧，难道是我供电的原因？再换外接电源

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-out-write.png"><img class="alignnone size-full wp-image-191" title="eSata-out-write" src="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-out-write.png" alt="" width="594" height="486" /></a>

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-out-read.png"><img class="alignnone size-full wp-image-190" title="eSata-out-read" src="http://sqh.me/blog/wp-content/uploads/2011/06/eSata-out-read.png" alt="" width="594" height="486" /></a>

看来eSata接口还真是不稳定。。我在使用电脑的情况下测试了一下我的电脑硬盘，让我郁闷了。

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/mydisk-read.png"><img class="alignnone size-full wp-image-194" title="mydisk-read" src="http://sqh.me/blog/wp-content/uploads/2011/06/mydisk-read.png" alt="" width="594" height="486" /></a>

。。。。。。。。。。让我情何以堪啊。。。

&nbsp;

&nbsp;

最后是我的U盘测试数据，正品果然是正品啊 。。。。。。

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/myU-disk-write.png"><img class="alignnone size-full wp-image-196" title="myU disk-write" src="http://sqh.me/blog/wp-content/uploads/2011/06/myU-disk-write.png" alt="" width="594" height="486" /></a>

<a href="http://sqh.me/blog/wp-content/uploads/2011/06/myU-disk-read.png"><img class="alignnone size-full wp-image-195" title="myU disk-read" src="http://sqh.me/blog/wp-content/uploads/2011/06/myU-disk-read.png" alt="" width="594" height="486" /></a>

这才是U盘啊啊啊啊啊啊，以前那个假的根本达不到啊。很好用。看来新蛋的东西还是有保障的。
