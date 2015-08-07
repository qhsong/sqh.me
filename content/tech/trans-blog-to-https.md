+++
Description = "实现https化"
Tags = ["http","https"]
date = "2015-08-07T10:57:56+08:00"
title = "本站实现全面https化"

+++

前几天一直说要把我的博客实现https化，一直没空。后来昨天被[ichon](https://ichon.me)一说，鼓动了一下，就把博客实现了https化。使用的是startssl SSL证书，把所有的http请求全部换成了https请求，妈妈再也不用担心我被劫持了。

然后是由于用的多说插件，多说评论的头像获取并不是https，导致文章页面浏览器显示的不是绿色，强迫症看了略蛋疼。凑合用用吧，等我的go语言版本的博客上线了，就干掉多说。

感谢[ichon](https://ichon.me)。
