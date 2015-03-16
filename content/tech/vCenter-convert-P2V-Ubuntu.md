+++
Description = "使用Center Converter转换Ubuntu到虚拟机中"
Tags = ["vCenter Converter","Ubuntu","转换","P2V"]
date = "2014-05-13T16:06:27+08:00"
title = "使用Vmware vCenter Converter进行P2V转换Ubuntu主机"

+++
最近小本性能太差，考虑将我的小本中的Ubuntu移植到小Y的虚拟机中。由于Vmware并没有提供直接能将物理机器转换成Workstation能够使用的虚拟机工具，但是他提供了通过vCenter Converter转换到Vmware EXSi，再从EXSi下载ovf模板下载到本地进行部署。

1、你要找一台Windows主机，能够访问到你的Ubuntu电脑和Vmware EXSi。在这台Windows主机上进行安装vCenter Converter。<span style="color: #000000;">vCenter Converter是免费的，直接去Vmware官网注册后即可下载。</span>

2、安装完成后，参照<a title="http://vaemon.com/article/1117.html" href="http://vaemon.com/article/1117.html">http://vaemon.com/article/1117.html</a>的方式进行安装。安装要点主要有：
<p style="padding-left: 30px;">i)  在Ubuntu上安装ssh服务器端并设置root管理员密码。</p>

<pre style="padding-left: 60px;" lang="bash">sudo apt-get install openssh-server</pre>
<pre style="padding-left: 60px;" lang="bash">sudo root passwd</pre>
<p style="padding-left: 30px;">ii)在Option的设置里，一定要把Reconfigure destination virtual machine的地方勾选去掉（vCenter 的Reconfigure脚本对Debein系列的支持不太好）</p>
        转移完成后，在EXSi中启动，必然是开不起来了。问题主要是在转换过程中，Vmware把硬盘分区的引导没有了，并且硬盘分区的uuid变了。导致Ubuntu无法启动，恢复方法可以参考<a title="http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2052628" href="http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2052628">http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&amp;cmd=displayKC&amp;externalId=2052628</a>

3、在虚拟机中挂载一张Ubuntu桌面版（服务器版也行) 的光盘，使用blkid命令查看新分区硬盘的uuid（需要加上sudo）。分别替换/etc/fatab和grub.cfg文件里面原来硬盘的uuid。（一定要替换的是原来硬盘的文件，所以要将原系统的分区挂载上来）

4、修复引导。具体参见我的 <a title="Ubuntu+win系列多系统重装修复" href="http://sqh.me/blog/?p=234">另外一篇文章</a>，安装grub，修复引导，即可启动成功。实现Ubuntu 的完美移植。

&nbsp;
