+++
Tags = ["PF_RING","Ubuntu","ntopng"]
date = "2015-03-31T17:01:43+08:00"
title = "Ubuntu下ntopng配置小记"

+++
```bash
$ sudo ntpdate time.buptnet.edu.cn #对时
$ svn co https://svn.ntop.org/svn/ntop/trunk/ntopng/
$ cd ntopng
$ rm .svn/ -r
$ sudo apt-get install autoconf automake libtool libcurl4-openssl-dev libsqlite3-dev libpcap-dev libcairo2-dev libpango1.0-dev libxml2-dev libnuma-dev
$ ./autogen.sh
$ make 
$ sudo make install
```
貌似对PF_RING有依赖，还在研究中。。重新make install PF
```bash
$ cd kernel
$ sudo make && make install
$ cd ../userland/lib
$ sudo make && make install
```

需要修改ntopng的makefile文件
在`LIBS`变量后面加入 `-lpfring -lnuma`

//TODO：需要安装redis server
```bash
$ sudo apt-get install redis-server
```

实际使用中发现CPU占用率过高，CPU占用率在65%左右，考虑可能使用的是libpcap库是原生的，并不是PF_RING重写的库，所以需要重新编译这个程序，使用PF_RING中的库。
重新编译PF_RING中的libpcap之后，效率提升10倍！CPU稳定在10%以内，以前是60%~70%。
重新编译PF_RING方法:
```bash
$ sudo apt-get remove libpcap* -y #卸载原有的pcap
$ cd PF_RING/userland/libpcap/
$ make 
$ sudo make install
$ ldconfig
```
再次重新编译ntopng即可。
目前暂时没有找到文档。
