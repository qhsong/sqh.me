+++
Description = "Ubuntu和windows7 多系统修复"
Tags = ["Ubuntu","Windows7","多系统","修复"]
date = "2011-06-19T10:09:02+08:00"
ds_title = "Ubuntu+win系列多系统重装修复"
title = "Ubuntu+win系列多系统重装修复"

+++
最近折腾小Y，在移动硬盘里装个win8 玩玩，又把自己原来的win7 32位换成了64位，很悲催的说。每次重装系统，都是折磨我这幼小的心灵。。

电脑以前是Ubuntu+win7+win XP三系统共存，采用的是先grub再mbr的引导方式。

每次重装完系统之后，很悲剧的就是grub不引导了。网上的修复办法很多，其中有一个是使用Ubuntu live，在终端里输入grub命令运行……经测试，根本无法执行grub。

最后还是在这里找到了良方。
```bash
sudo -i    #换到root下

fdisk  -l   #查看ubuntu系统安装盘符，或者是/boot的挂载点，我的在/dev/sda1

mount /dev/sda1  /media/mnt

grub -install --root-directory=/media/mnt  /dev/sda   #接下来重启系统（ubuntu），继续修改

sudo  update-grub2
```
到此ubuntu的系统启动引导就算完了。
其实发现一个问题，自己以后安装系统的时候，还是分出一个区域专门挂/boot把，要是那个分区出问题，整个电脑又启动不起来了。。。。。。win的安装机制也有点太那个了，非得 把你主引导的设置成活动的，把其他的 直接设置成非活动……

总体来说，今天还是挺和谐的。装了个U盘win8，又把自己电脑系统换成了win7 64位。。
