+++
Categories = []
Description = "量产kingston DT101 G2 u盘"
Tags = ["kingston","U-disk","DT101-G2","production","phison-PS22"]
date = "2011-02-21T18:07:00+08:00"
ds_title = "kingston DT101 G2 量产记"
title = "kingston DT101 G2 量产记"

+++
这几天突然想量产我的U盘，我的U盘同时也经历的起死回生的过程，很多人问我什么叫量产。知道的跳过，不知道的看看。

什么是量产（节选自百度百科—量产词条）

量产软件英文<strong>USB DISK PRODUCTION TOOL</strong>，简称是PDT,意思是U盘生产工具。U盘生产厂家不像我们玩家，一次就搞一两个U盘，他们生产都是按批计算的，是用电脑连上USB HUB，同时连上8-16个（甚至更多）U盘，然后用PDT向众多U盘写入相同数据，完成U盘生产的最后工序。所以PDT因此得名量产软件—即工厂大批量生产U盘的专用软件。 　　

量产软件从工厂流入玩家手中后，大家发现量产软件的功能是向U盘写入相应数据，使电脑能正确识别U盘，并使U盘具有某些特殊功能。U盘是由主控板+FLASH+外壳组成的，当主控板焊接上空白FLASH后插入电脑，因为没有相应的数据，电脑只能识别到主控板，而无法识别到FLASH，所以这时候电脑上显示出U盘盘符，但是双击盘符却显示没有插入U盘，就像是插入一个空白的读卡器。事实上这时候的U盘几乎就是读卡器。所以要让电脑识别出空白FLASH这张“卡”就要向FLASH内写入对应的数据，这些数据包括U盘的容量大小，采用的芯片（芯片不同，数据保留的方式也不同），坏块地址（和硬盘一样，FLASH也有坏块，必须屏蔽）等等，有了这些数据，电脑就能正确识别出U盘了。而当这些数据损坏的时候，电脑是无法正确识别U盘的。当然有时候是人为的写入错误数据，像奸商量产U盘的时候，把1G的U盘的FLASH容量修改为8G,插上电脑，电脑就错误的认为这个U盘是8G，这就是奸商制造扩容盘的原理。 　

　电脑正确识别出U盘后，玩家还发现PDT还有其他的功能，可以把U盘生产成各种特殊用途的U盘，比较常用的就是分区功能，把1个U盘分成数个移动盘；启动功能—使U盘能模拟USB CD-ROM,USB-ZIP，这个是目前用的最多的功能，加密功能—划出专门的加密分区。 　　

量产软件的功能使得它的用途一分为2:一是U盘恢复（不是数据恢复，是底层硬件信息的恢复），使因为底层硬件信息受损电脑无法识别的U盘重新被电脑识别出来，很像是MP3的固件恢复。。所以很多人的U盘电脑不认了，别人叫他量产一下就是这个意思。第二个用途就是模拟USB-CDROM，这点就是djhcszdh原文所说的。很多人说的量产指的就是把U盘划出个专用空间，模拟成USB-CDROM，然后载入自己喜欢的ISO镜像，这样维护和装机都比用真的光盘方便。这算是U盘DIY的高级玩法。现在很多U盘都支持3驱3启动，也就是1个U盘分成3个区，每个区模拟成一个驱动器，像笔者用的KINGMAX U-DRIVER 8G盘用SK6281主控，分区的时候就分成一个USB-HDD,一个USB-ZIP,一个USB-CDROM，分别加载不同的启动镜像，启动时选择相应的驱动器就能载入对应的启动镜像，相当于带了3张不同的启动光盘，很方便。 　　

综上所述，量产虽然是一个词，但指的却是2个不同的功能—恢复U盘或者是DIY启动U盘，大家看实际情况来分辩它的意思。 　　要注意的是量产软件是和主控相对应的，什么型号的主控就用什么量产软件，选错量产软件或者无法量产或者量产错误。大家最好用chipgenius的检测U盘的主控制芯片,有条件的最好能拆开U盘看看主控型号，然后下载相对应的量产工具和相应的教程，看懂了再动手。

一、量产的首次失败。

通过chipgenius检测到，我的U盘的主控芯片是phison PS22的。在网上下载到了一个量产工具，UP19_CTool_90AP_v1.05.exe,我设置的时候还把vid，pid改了，结果进行到最后提示failed，郁闷至极。再次插上U盘，电脑不能识别。

经过查阅资料，发现短接flash的data针脚可以重新识别。大晚上的，翻箱倒柜找了点工具，还是不行，晚上求助论坛，打算第二天再弄。

<address><a href="http://bbs.wuyou.com/viewthread.php?tid=187411&amp;extra=page%3D1">http://bbs.wuyou.com/viewthread.php?tid=187411&amp;extra=page%3D1</a></address><address></address>第二天来了，先去买工具，工具如下：

usb延长线一根，镊子一把，并下载到UP19_CTool_90AP_v2.08B9.exe，以及图一张
<a href="http://sqh.me/blog/wp-content/uploads/2011/02/U盘维修2.jpg"><img class="alignnone size-full wp-image-122" title="U盘维修2" src="http://sqh.me/blog/wp-content/uploads/2011/02/U盘维修2.jpg" alt="" width="478" height="327" /></a>

方法：1、在电脑上运行UP19_CTool_90AP_v2.08B9.exe

2、拆开U盘，将镊子放在flash29-30针脚，就是图中的d0和d1针脚之间，等到系统识别U盘再放开（上面有个小脚为第一针脚，别反了）

3、在UP19_CTool_90AP_v2.08B9.exe看到U盘之后，先刷回正常模式，再去考虑其他模式。下面是普通模式的配置：
<a href="http://sqh.me/blog/wp-content/uploads/2011/02/1.jpg"><img class="alignnone size-full wp-image-123" title="1" src="http://sqh.me/blog/wp-content/uploads/2011/02/1.jpg" alt="" width="704" height="541" /></a>

经过漫长的等待，U盘起死回生了，好了，开始下一步量产。

初步打算量产一个cdrom和一个removable disk。

顺便DIY一下U盘名字。

量产配置如下示：
<a href="http://sqh.me/blog/wp-content/uploads/2011/02/量产CDROM配置.jpg"><img class="alignnone size-full wp-image-124" title="量产CDROM配置" src="http://sqh.me/blog/wp-content/uploads/2011/02/量产CDROM配置.jpg" alt="" width="708" height="545" /></a>
点击开始，U盘灯一阵狂闪，半小时后，量产成功，看看我的成果：
<a href="http://sqh.me/blog/wp-content/uploads/2011/02/量产成功2.jpg"><img class="alignnone size-full wp-image-126" title="量产成功2" src="http://sqh.me/blog/wp-content/uploads/2011/02/量产成功2.jpg" alt="" width="406" height="141" /></a>

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/量产成功.jpg"><img class="alignnone size-full wp-image-125" title="量产成功" src="http://sqh.me/blog/wp-content/uploads/2011/02/量产成功.jpg" alt="" width="311" height="166" /></a>

电脑识别出一个光驱和一个U盘，光驱我刷的是小马的winpe，我尝试去刷大的iso文件，可是打不开，只好作罢……

至此，量产完毕，下面就是将U盘外壳用胶装上，over。
大家看看我的战场啊:

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0104.jpg"><img class="alignnone size-large wp-image-132" title="IMAG0104" src="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0104-768x1024.jpg" alt="" width="768" height="1024" /></a>

U盘反面，只有一个flash颗粒

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0103.jpg"><img class="alignnone size-large wp-image-131" title="IMAG0103" src="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0103-768x1024.jpg" alt="" width="768" height="1024" /></a>

U盘正面，小的那个是主控，大的是flash颗粒，短路就是要短路大的flash颗粒了。

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0102.jpg"><img class="alignnone size-large wp-image-130" title="IMAG0102" src="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0102-1024x768.jpg" alt="" width="1024" height="768" /></a>

被拆得四分五裂的U盘

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0101.jpg"><img class="alignnone size-large wp-image-129" title="IMAG0101" src="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG0101-1024x768.jpg" alt="" width="1024" height="768" /></a>

工具

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG01001.jpg"><img class="alignnone size-large wp-image-128" title="IMAG0100" src="http://sqh.me/blog/wp-content/uploads/2011/02/IMAG01001-768x1024.jpg" alt="" width="768" height="1024" /></a>

晚上拍的

<a href="http://sqh.me/blog/wp-content/uploads/2011/02/Copy-of-IMAG0101.jpg"><img class="alignnone size-large wp-image-127" title="Copy of IMAG0101" src="http://sqh.me/blog/wp-content/uploads/2011/02/Copy-of-IMAG0101-1024x768.jpg" alt="" width="1024" height="768" /></a>

工具二
