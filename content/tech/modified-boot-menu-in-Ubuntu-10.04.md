+++
Description = "修改Ubuntu10.04默认启动菜单"
Tags = ["Ubuntu","启动菜单","默认"]
date = "2012-01-15T23:53:23+08:00"
ds_title = "[转]修改Ubuntu 10.04 启动菜单默认系统的三种简便易用的方法总结"
title = "[转]修改Ubuntu 10.04 启动菜单默认系统的三种简便易用的方法总结"

+++
博客地址：<a href="http://www.cnblogs.com/cubean/archive/2010/08/02/1790286.html">http://www.cnblogs.com/cubean/archive/2010/08/02/1790286.html</a>

一、<strong>能上网情况下的最好方法：安装startupmanager</strong>——图形启动项管理器

打开终端输入：sudo apt-get install startupmanager
安装启动管理器
系统——系统管理——启动管理器，选中默认开机操作系统即可。

（编者注）或者安装：

安装命令如下：
sudo add-apt-repository ppa:danielrichter2007/grub-customizer
sudo apt-get update
sudo apt-get install grub-customizer

（/编者注释完毕）

二、<strong>最方便的方法：调整Windows系统的默认启动顺序（不需要修改grub.cfg）</strong>

/etc/grub.d 目录中的脚本文件的文件名都是以数字开头，这确定了在执行update-grub 时各文件内容被执行的顺序。

1. 用 sudo nautilus 命令，以root权限打开文件浏览器。然后找到30_os-prober这个文件，把 30_os-prober这个文件名的数字30改为05到10之间的数字即可（没多少可选06、07、08、09），比如改为08_os-prober，这样创建出来的grub.cfg内的菜单项，windows的排序就会自动在ubuntu之前；

2. 运行 sudo update-grub 命令。就可以看到，windows7 已经排在了第一个的位置上。

&nbsp;

<strong>三、网上介绍最多的方法（修改grub.cfg）</strong>

用 sudo gedit  /boot/grub/grub.cfg 命令打开grub.cfg 这个文件，

一种方法是并将文件中的 set default="0" 中的 0 改成对应欲默认启动的操作系统的序号(从0开始) 。然后保存；

另一种方法是把Windows对应的启动说明那一段剪切到启动说明段最前面；

用这2种方法都能默认启动Windows，但用修改grub.cfg 文件来实现修改默认启动项应注意：当使用update-grub 命令或者系统升级、grub升级等，grub.cfg文件将被复原。需要重新修改。

如果懒于每次update-grub 之后都要修改grub.cfg 文件，则可以进行如下操作：

用 sudo gedit /etc/grub.b/00_header 命令打开00_header 这个文件，找到 ser default ="${GRUB_DEFAULT}" 一行，将${GRUB_DEFAULT} 换成欲默认启动的操作系统的序号（从0开始），保存，再update-grub。这样每次update-grub之后生成的grub.cfg将自动改成我们想要的样子。
