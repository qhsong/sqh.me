+++
Description = "理解IPv6"
Tags = ["IPv6","简介"]
date = "2015-01-28T20:52:11+08:00"
title = "理解IPv6（1）：介绍"

+++
v4局限
========
* IP地址耗尽、路由表较大、简洁配置（DHCP）、IP级别安全、QoS
* NAT转换、NAT编辑器（对IP数据包进行改动，如FTP中的IP地址转换）

v6特性
==========
* 新的协议头格式（简化协议头）
* 巨大地址空间
* 有效的、分级的寻址和路由结构
* 有状态（DHCP）、无状态（自动配置链路地址）的地址配置
* 更好的QoS（对流进行处理）
* 新协议处理邻节点交互（NDP 取代ARP、ICMPv4）
* 可拓展性

v6术语
========
* 节点
* 路由器：转发不以它为目标的数据包节点
* 主机：不转发以它为目标的数据包节点
* 上层协议：传输层协议TCP/UDP + Internet层协议ICMPv6
* 局域网段：链路的一部分、单一介质、以网桥或者二层交换为边界
* 链路：以路由器为边界的一个或多个局域网段
* 子网：使用相同的64位IPv6地址前缀的一个或多个链路、可以被内部路由器分为几个部分
* 网络：路由器连接起来的两个或者多个子网
* 邻节点：同一链路上的节点
* 接口：一个链路上的物理或者逻辑节点
* 地址：IP地址
* 数据包：IPv6的PDU（协议数据单元）
* 链路MTU：一个链路上发送的MTU
* 路径MTU：源节点到目标节点路径上，不执行主机拆分的情况下可发送最大长度IPv6包

解决问题
=======
* IP地址耗尽、国际地址分配
* 重建端到端通讯（没有NAT）
* IPv6区别地址选择（可以根据不同作用域进行地址选择）
* 更好的转发（转发过程中字段处理较少）
* 内置移动性和安全性

win Server 2003种的IPv6
=========
* 支持特性
     - 基本协议栈：单播、多播、泛播寻址；ICMPv6、ND、MLD协议；无状态的地址自动配置；支持移动IPv6中的通信节点
     - 6to4：
     - ISATAP协议：站点内自动隧道寻址协议（纯v4体系的节点与v6其他节点进行通讯）
     - 6over4：IPv4多播隧道（把IPv4网络体系作为一个支持多播的逻辑链路）
     - 端口代理协议(PortProxy)：不能使用通常IP层协议进行连接的节点和应用程序能够相互通讯
     - 临时地址：随机获得接口标识、过段时间会改变
     - DNS支持：AAAA、IPv6网络中发送DNS通讯流（默认FEC0:0:0:FFFF::1/FEC0:0:0:FFFF::2/FEC0:0:0:FFFF::3）、v4或v6种将IPv6主机AAAA记录动态注册到DNS中
     - IPSec 支持：
     - 静态路由器支持：转发IPv6包、发送路由器公告（有路由表发布时）
     - 地址选择：netsh interface ipv6 show prefixpolicy（查看默认前缀策略表）
     - 路由器公告中的站点前缀：已发布链路上配置一个站点前缀长度
* 应用程序支持
     - balabala
     - SNMP MIB(RFC draft)
* API
     - socket
     - RPC
     - IP助手
     - winInet（windows Internet扩展）
     - .Net扩展
* IPv6工具
     - ipconfig
     - Route
     - Ping
     - tracert
     - pathping（路径网络延迟和丢包信息，比较慢）
     - Netstat：同linux netstat
     - netsh interface ipv6
          + show interface：接口列表
          + show address：地址列表
          + show routes：ipv6路由
          + show neighbors：邻节点缓存
          + show destinationcache：目标缓存内容
          + 6to4
          + isatap
          + teredo （本书尚未涉及）
