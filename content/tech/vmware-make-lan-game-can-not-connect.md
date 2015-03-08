+++
Description = "安装vmware 局域网 游戏"
Tags = ["vmware","局域网","游戏"]
date = "2011-06-01T17:59:44+08:00"
title = "装了VM虚拟机导致局域网游戏不能运行"

+++
<p>最近因为要做网页，所以装了个VM虚拟机，结果某天晚上玩宿舍打算集体CS一把的时候，关键时刻，我电脑怎么设置，怎么刷新都连不上网，怎么设置都行不通。。。。。我差点就想重启路由了。。。</p>
<p>后来冷静下来，看到网络与设置中心，看到这么一个画面，我是以多重网络的形式访问Internet的</p>
<a href="http://sqh.me/blog/wp-content/uploads/2011/06/网络问题.jpg"><img src="http://sqh.me/blog/wp-content/uploads/2011/06/网络问题.jpg" alt="" title="网络问题" width="553" height="342" class="alignnone size-full wp-image-209" /></a>
<p></p>
<p>这和我之前用的是直接连接互联网访问的类型不同。这个被VM的多重虚拟的网卡进行了数据的交换。</p>
<p>于是乎，我关闭了VM产生的网卡。问题得以顺利解决。</p>
<a href="http://sqh.me/blog/wp-content/uploads/2011/06/网络问题2.jpg"><img src="http://sqh.me/blog/wp-content/uploads/2011/06/网络问题2.jpg" alt="" title="网络问题2" width="757" height="166" class="alignnone size-full wp-image-210" /></a>
<p></p>
<a href="http://sqh.me/blog/wp-content/uploads/2011/06/网络问题3.jpg"><img src="http://sqh.me/blog/wp-content/uploads/2011/06/网络问题3.jpg" alt="" title="网络问题3" width="550" height="242" class="alignnone size-full wp-image-211" /></a>
<p></p>
<p>后来一想，win7的多重网络是能够让多个网卡为网络加速。但是其使用的是ip映射的方式。这样一来，我就和我的虚拟机组成了内网，而宿舍的路由器就是外网了。</p>
<p>最近在查资料，win7这个多重网络还真麻烦。。未完待续。。。</p>
