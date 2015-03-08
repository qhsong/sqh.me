+++
Description = "配置samba作为PDC域服务器"
Tags = ["centos","samba","PDC"]
date = "2012-05-13T13:11:33+08:00"
ds_title = "centos中配置samba作为PDC域服务器"
title = "centos中配置samba作为PDC域服务器"

+++
饭饱神虚，前几天手贱，把我们的服务器装上了个centos，还没有显卡驱动。干什么事情都要在text-mode，这几天由于需要，装了个samba，光配置就花了好几天，下面把配置samba的各种过程记录下来，以供大家查询。

如何配置samba的事情，我想之前有很多文章了，我参考的是

1.<a href="http://www.5ilinux.com/2003/11/samba3-pdc.html">samba 3.0 轻松搞定PDC域服务器</a>
2.<a href="http://hbyxfy.iteye.com/blog/469963">一步步学习samba</a>
3.<a href="http://yuanbin.blog.51cto.com/363003/122514">samba之PDC配置</a>
4.linux下用户权限和文件权限相关管理文件。

&nbsp;

问题一：启动samba服务，无法客户机无法加入

启动完成后，无论怎么设置，都无法加入。最后经过排查，是防火墙把所有进入的数据阻挡了。一气之下，把防火墙关了。

&gt;&gt;setup

防火墙配置把enable前面的*去了。service 里把iptables这个服务前面的*去了。

&gt;&gt;service iptables stop

问题解决。

&nbsp;

问题二：在所有文件夹为777的权限之下，登录提示无法创建配置文件，登陆后发现共享磁盘不能写。

<a href="http://gdzy1987.blog.51cto.com/681139/148024">samba文件不能写</a>

原因是SElinux阻止了，关闭SElinux就可以解决问题了。

&gt;&gt;vi /etc/selinux/config

把SELINUX改为disabled就行了，彻底关闭之后，就可以访问了。
