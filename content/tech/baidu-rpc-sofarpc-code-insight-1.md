+++
Categories = ["sofa-pbrpc", "源码“, "分析"]
date = "2017-05-26T22:43:01+08:00"
title = "sofa-pbrpc源码分析(1)"
Description = "sofarpc 源码分析"
Tags = ["sofa-pbrpc", "源码", "C++", "boost"]
draft =  true
+++

## 介绍
(sofa-pbrpc)[https://github.com/baidu/sofa-pbrpc]是百度开源的一个C++ RPC库，使用Google Protobuf作为序列化协议，Asio网络库开发的一个C++ RPC库，现在有很多公司也都在使用。用他来学习boost::asio，C++11的一些新语法是个不错的选择。
计划会通过一系列的文章来研究这个库，并且学习他的一些用法，以及C++语法的笔记。

一个典型的服务器端程序如下所示：
```C++
int main() {
    SOFA_PBRPC_SET_LOG_LEVEL(NOTICE);

    sofa::pbrpc::RpcServerOptions options;
    options.work_thread_num = 8;
    sofa::pbrpc::RpcServer rpc_server(options);

    if(!rpc_server.Start("0.0.0.0:12321")) {
        SLOG(ERROR, "start server failed");
        return EXIT_FAILURE;
    }

    sofa::pbrpc::test::EchoServer *echo_service = new EchoServerImpl();
    if (!rpc_server.RegisterService(echo_service)) {
        SLOG(ERROR, "register service failed!");
        return EXIT_FAILURE;
    }

    rpc_server.Run();

    rpc_server.Stop();

    return EXIT_SUCCESS;
}
```

`RpcServerOptions`类里面主要是启动server的一些参数，位于[rpc_server.h](https://github.com/baidu/sofa-pbrpc/blob/master/src/sofa/pbrpc/rpc_server.h#L23)文件中，主要定义的是Server的启动参数，如工作线程数量、io_service 池大小、keep_alive时间等。

## RpcServer类




