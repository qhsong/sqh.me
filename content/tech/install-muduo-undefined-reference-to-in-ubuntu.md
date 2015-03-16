+++
Description = "安装muduo库出现问题"
Tags = ["muduo","Ubuntu","Undefined"]
date = "2014-06-03T11:49:26+08:00"
title = "ubuntu编译安装muduo网络库undefined reference to 问题"

+++
安装muduo网络库时，一开始用默认配置，string类型是__gun_cxx::__sso_string，和google protocol buffer的网络类型不兼容。然后就重新编译了一下muduo库，结果导致了如下的错误：
```bash
../../../lib/libquery_proto.a(query.pb.cc.o): In function `google::protobuf::GoogleOnceInit(int*, void (*)())':
/usr/local/include/google/protobuf/stubs/once.h:127: 
undefined reference to `google::protobuf::GoogleOnceInitImpl(int*, google::protobuf::Closure*)'
collect2: ld 返回 1
make[2]: *** [bin/protobuf_client] 错误 1
make[1]: *** [examples/protobuf/codec/CMakeFiles/protobuf_client.dir/all] 错误 2
make: *** [all] 错误 2
```

通过跟踪CMakeList的参数，发现的问题。默认的google protocol buffer动态安装库在/usr/local/lib，而不在编译器的输出路径当中。需要自己增加链接库文件。由于自己对CMake不是很熟悉。所以我直接把CMakeList.txt文件中的CXX_FLAGS变量，把`pkg-config --cflags --libs protobuf`的输出结果加进去，再次运行build.sh。 安装muduo网络库即可。

========2014年11月6日==============

出现这个问题大部分的原因是protobuf的动态链接库的位置没有指定。So，请在参数中指定你的动态链接库。
