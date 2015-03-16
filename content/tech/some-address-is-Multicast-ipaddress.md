+++
Description = "多播地址"
Tags = ["IP","多播地址"]
date = "2013-05-31T13:18:10+08:00"
title = "224.0.0.22 224.0.0.252是多播地址"

+++
<pre>今天在查ARP表的时候发现了几个不太正常的ip地址是224.0.0.22 224.0.0.252，在查询一番信息之后找到了：
这个IP是个多播地址:

Host Extensions for IP Multicasting [RFC1112] specifies the extensions
required of a host implementation of the Internet Protocol (IP) to
support multicasting.  The multicast addressess are in the range
224.0.0.0 through 239.255.255.255. Current addresses are listed below.
The range of addresses between 224.0.0.0 and 224.0.0.255, inclusive,
is reserved for the use of routing protocols and other low-level
topology discovery or maintenance protocols, such as gateway discovery
and group membership reporting.  Multicast routers should not forward
any multicast datagram with destination addresses in this range,
regardless of its TTL.

<a href="http://bbs.sjtu.edu.cn/bbscon,board,Windows,file,M.1015599539.A.html">http://bbs.sjtu.edu.cn/bbscon,board,Windows,file,M.1015599539.A.html</a>

学习了。。。
看了网上的一些说明，224.0.0.22主要是用来IGMP网际管理协议。看了我们这个路由器还是蛮高级的。。
<a href="http://www.antionline.com/showthread.php?261762-Who-knows-the-224.0.0.22">http://www.antionline.com/showthread.php?261762-Who-knows-the-224.0.0.22</a>
224.0.0.252主要是Link-local Multicast Name Resolution，一个基于DNS的协议，大可不必担心。。。
看来网络这一层还是要多了解了解才行啊。。。</pre>
