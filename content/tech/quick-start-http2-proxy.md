+++
date = "2017-09-24T18:36:52+08:00"
title = "使用Caddy快速搭建http2 proxy"
Tags = ["http2", "proxy", "caddy"]
+++

## Caddy介绍
[Caddy](https://caddyserver.com/)是一个使用Golang开发的http服务器，其主打的特点就是快速支持https和HTTP2。本站的博客也是使用它搭建的，详细搭建过程可以[参考这里](https://sqh.me/tech/host-hugo-blog-using-caddy/)。最近在其更新的[0.10.7](https://caddyserver.com/blog/caddy-0_10_7-released)中，支持了Froward Proxy，Chrome浏览器也内建了对HTTP2 proxy的支持。


Caddy的Proxy支持一下功能：

* HTTP2 Proxy
* Probe resistance，当验证错误时，不会返回HTTP 407 错误。当有Probe进行探测时，其不会就直接暴露出自己是一个Proxy（实验阶段，比较有意思）
* Basic Auth
* IP hiding
* Servers PAC file
<!--more-->
## 步骤

* 准备

为了支持HTTTPS和HTTP2，首先需要准备一个域名以及服务器。

* 下载Caddy

Caddy的下载页在[这里](https://caddyserver.com/download)，他是按照你选择的操作系统和插件类型来下载二进制binary（有点好奇它是怎么做到的，穷举么）。在下载选择的插件时，一定要选上`http.forwardproxy`，这样在下载的二进制包中才会包含这个功能。对于Linux下的下载地址见可以直接点击[此处](https://caddyserver.com/download/linux/amd64?plugins=http.forwardproxy&license=personal)。



* 解压安装

下载后的文件是个tar包，解压并把他放到任何你想放的地方。里面就一个Caddy的可执行文件。

* 编写CaddyFile配置文件

Caddy的配置是靠一个CaddyFile的配置文件来进行配置的，其配置语法非常简单，和Nginx的配置文件很类似。如下就是一个最简单的配置文件：
```
http://example.com:443 {
gzip
log access.log
forwardproxy
}
```
可以看到直接将域名和端口写到配置文件中，在对于的配置块中，我们打开了gzip选项，启用了log日志以及forwardproxy功能。
详细的forwardproxy功能详见[forwardproxy](https://github.com/caddyserver/forwardproxy)，上面提到的功能配置项都有了。

**NOTICE**:由于启动时需要从LCE获取HTTPS证书，所以首次启动需要将本地443端口的程序关闭，获取到证书之后就可以开启了。

* 运行Proxy程序

```bash
$ ./caddy --conf Caddyfile
```
运行起来之后，就可以在浏览器中设置HTTP2的访问了。对于`SwitchyOmega`而言，代理协议写HTTPS，然后就能正常使用Proxy了。

* 设置驻守

可以采用supervisord把这个程序监控起来，失败了就重启。具体可以看supervisor的相关资料。

## FIN
依托LCE和Caddy内建的HTTP2功能，在加上Caddy简单易用的插件开发功能，使得我们可以抛弃nghttpx和Squid这一套，直接就能够使用的HTTP2 Proxy。

还记得一开始折腾HTTP2 Proxy的痛苦，现在不用了，直接一次性搞定。让我畅游HTTP2。感谢Caddy和[Sergey Frolov](https://sfrolov.io/)。
