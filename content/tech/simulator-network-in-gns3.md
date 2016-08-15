+++
Categories = ["network", "simulator", "gns3","software"]
Description = "simulator network in gns3"
Tags = ["network","gns3"]
date = "2016-08-15T21:48:10+08:00"
title = "使用Gns3和EXOS进行网络模拟"

+++

[gns3](https://www.gns3.com/)是一个开源的网络模拟器，类似PacketTracer。它支持很多种设备，可以把Docker container、Virtual box、VMware以及思科各种系列（除了Nexus系列）的交换机和路由器导入到模拟器中，进行网络组网测试。目前有很多家公司都在用它来进行网络模拟。它也可以用于CCNA考试里的网络模拟。

## 安装
gns3可以安装在多个操作系统中，支持Windows，Linux，OSX等，具体安装文档参见[这里](https://www.gns3.com/support/docs/quick-start-guide-for-windows-us)。它分为两部分，一部分是模拟器的核心部分，另外一部分是Gui作为客户端，是用Qt写的。直接安装的话就是安装它的`all-in-one`的版本，将两个部分安装到一起。
当然，这两个部分也可以分别安装。我比较推荐的是将模拟器核心安装到虚拟机中，官方提供了VirtualBox和VMware的Ovf文件，可以直接作为模板导入到虚拟机中。安装时不推荐安装到VMware player中，一定要安装在Vmware Workstation里面，因为Player的网络功能太差，很多东西功能后面没法用。可以直接到(这里)[https://github.com/GNS3/gns3-gui/releases]下载对应版本的客户端和服务器。它对Ubuntu系列的支持比较好，Redhat系列支持不太好，很多包都还是不可用，不推荐在CentOS下安装。
它同时还可以将服务器部分部署到ESXi中，具体我没试过，如果主机是Doker的话，在ESXi中应该很方便。但是如果主机是VirtualBox或者Vmware的话，ESXi可能便利性可能没那么高。
安装完成之后就可以按照文档提示导入相关的镜像和主机即可，所有的配置都在`GNS3 preferences`中完成。
如果你觉得思科的交换机用的不爽，有一些高级功能没有的话，你可以试试EXOS的交换机镜像。安装方法在[这里](https://github.com/extremenetworks/Virtual_EXOS/blob/master/GNS3_EXOS-VM_Guide.md)。这个文档总结了Gns3常用的安装方法，可以看看。
在配置Docker时，如果是在虚拟机里面，则需要去虚拟机里面安装Docker images。EXOS包含很多高级功能，如LLDP，DCBX。
## 实验
我自己简单的做了一个拓扑入下图所示：
![Screenshot+from+2016-08-01+17-37-04](https://qhsong.blob.core.windows.net/qhsong-blog/2016/08/Screenshot+from+2016-08-01+17-37-04.png)
共有4个节点，中间的节点是EXOS交换机，其余两个是Docker container，还有一个VirtualBox虚拟机。他们都连到了同一个交换机上，互相能ping通。

看到gns3，还是很惊艳。网络模拟器也能做成这样。对网络模拟器有需求的同学可以看看。


