+++
title = "Yubikey 4 入手心得和折腾体会"
Description = "Yubikey 4 入手 使用"
Tags = ["Yubikey", "OTP", "使用"]
date = "2016-10-15T21:16:48+08:00"
+++

## Yubikey 4 介绍

Yubikey是国外一家公司出的用来做OTP的一个硬件设备，同时它也是一个GPG 的smart card。买它的原因是因为公司需要我们使用Github的人都最好有一个GPG Key对我们的Commit做个签名，而证书的管理是个难题，证书拷过来拷过去的也就没有什么安全性可言了。这东西在Tuna那边已经被安利了好久了，于是就买了。结果发现玩的根本停不下来，太有意思了。OTP功能到哪里都适用，最近都把我的ssh登陆换成了使用Yubikey来认证，感觉比我那个证书登陆不知道高到那里去了。

## Yubikey 4功能
Yubikey 4主要有两个功能，一个功能是OTP和两步验证，另外一个功能是GPG smart card。

OTP是One time password 的简称。Yubikey插入系统之后默认是一个HID设备。轻轻触碰以下就能生成一个字母数字组成的密码。Linux下可以使用yubikey PAM模块对这个密码进行验证，问题就是密码验证需要在线完成，电脑要是在不能上网的地方就比较蛋疼，所以最好在服务器上使用。
同时，ssh也可以通过GPG证书的方式来进行验证，所以实际上连入系统的认证方式是有两种的，一种是基于OTP的两步验证机制，另外一种就是基于GPG证书的认证机制，各有利弊吧，这个还要看具体的使用场景。

两步验证就非常有意思了，目前支持的网站有Gmail、Github、Dropbox等。之前每一次登陆Gmail都要到处找手机去接受验证码，现在就不用了，直接触碰一下Yubikey就能正常登陆了，比较方便。缺点就是只支持Chrome浏览器，Firefox貌似需要折腾一下，我还没去折腾。WordPress也提供了相关的yubikey插件在登陆的时候要做两部认证。

GPG smart card解决了我需要在多台电脑中sign文档的问题，随身就带着一个GPG 认证设备，需要的时候直接sign一下也是很爽。现在我的所有Git commit 都经过了GPG sign。今天更新了我的博客，把我的GPG fingerprint更新了，欢迎大家签名我的证书。

总体来说用户体验还是很好的，就是要做好备份工作，哪天丢了就GG了。

还有一个额外的功能是可以做为macOS和Windows下的认证登陆密钥，目前mac登陆的时候如果插上Yubikey的画就直接使用Yubikey的PIN登陆。不支持Linux有点蛋疼。。估计还需要一些调教。
