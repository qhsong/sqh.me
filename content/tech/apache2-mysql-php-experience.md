+++
Description = "apache2 mysql php使用的一些心得"
Tags = ["apache2","mysql","php","心得"]
date = "2011-05-12T14:31:38+08:00"
ds_title = "apache2+mysql+php心得"
title = "apache2+mysql+php心得"

+++
最近在折腾php，一开始想着是用IIS+php+mysql的，可是一直安装了一天，最后放弃。采用apache2+php+mysql。

安装中参考了<a href="http://www.jb51.net/article/17267.htm">http://www.jb51.net/article/17267.htm</a>

最后自己还是卡在了apache2上，最后发现，是在apache2配置里面，有那么一句话：

phpdir""我没配置，配置完成后。所有安装完成，over。

最后找到这个方式是正确的。<a href="http://blog.fesite.com/2007/10/15/windows-apache-php-mysql/">http://blog.fesite.com/2007/10/15/windows-apache-php-mysql/</a>
