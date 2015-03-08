+++
Description = "Y460在Ubuntu下安装双显卡切换驱动"
Tags = ["Ubuntu","10.04","Y460","双显卡","切换"]
date = "2012-10-25T12:23:46+08:00"
ds_title = "Ubuntu 10.04 安装Y460 双显卡切换"
title = "Ubuntu 10.04 安装Y460 双显卡切换"

+++
这几天一直在折腾ubuntu，就为了他的一个显卡切换功能。自己用的小Y，显卡是i3集成显卡以及HD 5650。以前一直不喜欢在ubuntu上工作，因为发热量太大了，吹风口直接很烫手。加上最近原来的ubuntu空间已经不够了，就决定自己重新装一下系统。

回归正题，安装Y460 ubtuntu驱动，先去官网上下载11.6的驱动。

这是ati驱动的wiki：<a href="http://wiki.cchtml.com/index.php/Ubuntu_Lucid_Installation_Guide">http://wiki.cchtml.com/index.php/Ubuntu_Lucid_Installation_Guide</a>

先安装依赖关系
<pre lang="bash">$ sudo apt-get install build-essential cdbs fakeroot dh-make debhelper debconf libstdc++6 dkms libqtgui4 wget execstack libelfg0</pre>
64位还要安装
<pre lang="bash">$ sudo apt-get install ia32-libs</pre>
然后使用

<pre  lang="bash">$sudo sh ./ati-deriver-installer.sh</pre>

安装

最后初始化一下就行了。

-----------------------------------------------分割线---------------------------------

经过我几天的测试，amd的驱动11.6的目前来说对于这个系统是最稳定的，没出什么问题。特效全开没问题，也不会死机。折腾了好几天，重新安装了好几遍ubuntu，可怜我的小电脑啊。。

附上这个版本的下载地址：

<a title="http://support.amd.com/us/gpudownload/windows/previous/11/Pages/radeon_linux.aspx?os=Linux x86&amp;rev=11.6" href="http://support.amd.com/us/gpudownload/windows/previous/11/Pages/radeon_linux.aspx?os=Linux x86&amp;rev=11.6">http://support.amd.com/us/gpudownload/windows/previous/11/Pages/radeon_linux.aspx?os=Linux x86&amp;rev=11.6</a>

看来不是越新的越好啊。。。
