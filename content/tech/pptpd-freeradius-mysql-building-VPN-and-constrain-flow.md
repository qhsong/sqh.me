+++
Description = "pptpd freeradius mysql VPN 限制流量"
Tags = ["pptpd","freeradius","mysql","VPN","限制流量"]
date = "2012-02-15T20:35:07+08:00"
ds_title = "pptpd+freeradius+mysql 构架VPN并进行流量限制笔记"
ds_url = ""
title = "pptpd+freeradius+mysql 构架VPN并进行流量限制笔记"

+++
去年就折腾了一下这个，一直没有折腾出来，今年在北京呆了几天，在情人节结束之后，到今天回到了秦皇岛，又折腾了一下午，总算把上述一套系统给做出来了。

主要参照

1、<a href="https://wangyan.org/blog/freeradius-pptp-l2tp-html.html">PPTP/L2TP + FreeRADIUS + MySQL 安装与配置</a>

2、<a href="http://www.lazylei.com/index.php/2010/11/centospptpdfreeradiusmysqlara-%E6%80%BB%E7%BB%93/">CentOS+pptpd+freeradius+mysql+ara 总结</a>

3、<a href="http://wiki.freeradius.org/">freeradius wiki</a>

....

步骤

1、安装pptpd参照资料2，资料2里面那个脚本文件已经相当详细，没有什么好说的，几乎是傻瓜化了的

2、安装freeradius，下载安装过程参照的是资料1里面，这样也能保证自己得到的是最新版本的。但是在测试完成之后，加入mysql测试的时候，输入`radiusd -X`，有一个提示是`Failed to add duplicate client localhost`，自己一直搞不懂为什么有个副本呢？后来把naslist.conf文件里面全部注释了，还是不行。

后来查资料发现，我原先为了安全，把test123改成了其他密码，本以为顺着做按照自己的想法改就行了。所以在naslist.conf还有后面的mysql里面输入的是我自己改的这个secret，不是test123，而radius的客户端没有改，所以主机就不认。改也很简单，
```bash
cd /usr/local/etc/raddb
vi clients.conf
```

找到这样的语句
```bash
client localhsot{

}
```
把这个语句块中的secret=test123改为你要的密码，这样，客户端和nas之前就能正常沟通了，也保证了安全的需要。

3、mysql部分

mysql对于用户的控制是在radcheck表里面，最简单的，用户名和密码控制就用User-Password ==这个属性就可以了，还有一个限制连接数的属性是Simultaneous-Use := ，不同的操作符会有不同的含义，具体参见wiki里面。

还可以用group来控制，这个可以将用户分组来做。大家可以看一下那个表的结构，大家也就知道怎么做了。

4、流量限制部分

我是把统计部分直接加到了sql/mysql/counter.conf文件末尾，所以，首先到radiusd.conf文件里面，找到$INCLUDE /sql/mysql/counter.conf前面的#去掉，然后

```bash
cd sql
cd mysql
vi counter.conf
```

在文件后面加入计算模块

```C
sqlcounter monthlyBytecounter{
counter-name = Total-Max-Octets
check-name = Max-Octets
reply-name = ChilliSpot-Max-Total-Octets
sqlmod-inst = sql
key = User-Name
reset = monthly
query = "SELECT (SUM(acctinputoctets)+SUM(acctoutputoctets)) FROM radacct WHERE UserName='%{%k}'AND UNIX_TIMESTAMP(AcctStartTime) &gt; '%b'"
}
```

同时，在/usr/local/raddb/sites-available/default文件里的authorize {} 节点里的最后（一定是最后，我之前加在开始就不行），加入这个模块的名字

<code>monthlyBytecounter</code>

然后重启radius，

<code>service radiusd restart</code>

好了，mysql里面用户又多了一个属性了，<code>Max-Octets</code>，op为:=，至此，radius系统部分搭建完成。剩下的就是大家围绕radius数据库发挥的空间了。

后记

写到现在已经是2012年2月15日20:30:25，说实话，有点累了，看着这个自己搭建的vpn，不知道怎么说才好。或许真是成就感吧。无线鼠标也没电了，今天好累，早点回去睡觉吧。秦皇岛的冬天，好冷好冷。从中学到了好多东西吧，看来，网络基本原理，还是要学学的。还有一些网上的帖子，自己也要思考一下原理，要不然差点就掉进了陷阱里了。

开始了秦皇岛的生活，明天，早安。

最近晚上有时会发烧，不知道是吃多了还是怎么的。

明天开始做电视台的服务器了。。。这几天还真是事情多。

这个情人节，陪陪晖晖，在北邮上课，挺好的，还能陪陪她。

&nbsp;
