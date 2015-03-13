+++
Description = "在DELL Powerdge 2950上安装ubuntu和hustoj"
Tags = ["DELL","Powerdge 2950","Ubuntu","hustoj"]
date = "2013-05-11T16:32:24+08:00"
title = "DELL PowerEdge 2950 安装ubuntu手记 hustoj"

+++
学校要架一个OJ系统，使用的是hustoj，之前INTEL送了一台DELL服务器，于是乎要把它装上ubuntu 10.04 server版本。
1、刻录安装ubuntu 10.04，一路下一步，服务器类型选择LAMP服务器，他会自己给你装好apache mysql php
2、配置网卡
进入系统后，ifcongfig只有一个lo回路。但是这台服务器有双网卡。查询lspci |grep Eth后发现认出了两张网卡，lsmod 也加载了Breadcom的驱动，就是不显示。

解决方法：
$cd /etc/network
$sudo vi interfaces
在interfaces根据要求修改
auto eth0
iface eth0 inet static #若为静态ip
address 10.90.0.1 #ip
netmask 255.255.255.0 #子网
gateway 10.0.0.1 #网关

auto eth1 #网卡2
iface eth1 inet dhcp #若为dhcp
好了之后保存修改
$sudo /etc/init.d/networking restart
这样ifconfig就能看到两张网卡啦

这个服务器因为没有两个网段，所以就只设置了第一个网卡，第二张网卡被停用了，要不然路由表冲突。
3.DNS
$vi /etc/resolv.conf
加入
nameserver dns的ip
就可以了。
4.安装HustOj
https://code.google.com/p/hustoj/w/list
参考WiKi中的README文档
主要是在svn check后，编辑install.sh的时候，不但要设置数据库用户名和密码，同时把yum命令换成apt-get，这样的话某些依赖包才能正常安装。基本上用它的脚本就没啥问题了。
5.安装了ssh，可以用ssh登录上服务器进行修改
