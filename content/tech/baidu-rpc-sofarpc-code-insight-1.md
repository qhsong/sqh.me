+++
Categories = ["sofa-pbrpc", "源码“, "分析"]
date = "2017-05-26T22:43:01+08:00"
title = "sofa-pbrpc源码分析(1)"
Description = "sofarpc 源码分析"
Tags = ["sofa-pbrpc", "源码", "C++", "boost"]
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
<!--more-->
## RpcServer和RpcServerImpl类
`RpcServer`类是整个库暴露的接口，所有的实现都由`RpcServerImpl`类来完成，PIMPL。`RPCServer`中只有指向`RpcServerImpl`的一个shared_ptr。

`RpcServerImpl`中含有一个`ServicePool`类用于管理所有的服务，`FlowController`类用于进行流控，`RpcListener`类处理TCP连接信息，`TimerWorker`类用于处理一些定时任务。`IOServicePool`是一个`ThreadGroup`池，`ThreadGroup`主要用于维护线程池和一个`io_service`。每一个`io_service`对象会被多个线程同时运行，这个也是一种比较高效使用`io_service`的方式。`IOServicePool`就维护了这样的一个Pool保证能快速的得到`io_service`。`WebService`提供了http的访问接口，`FastLock`为一个内部使用的锁。

`RpcServerImpl::Start`是对这些类的一个初始化，`_maintain_thread_group`创建了一个线程来处理RpcServer的自身调用（暂时不知道是啥，后来再补充）。`_io_service_pool`就是供rpc service调用的Pool，这个Pool的大小和线程数均可以自己指定。

在初始化完了上述基本的io_service池后， 就需要对监听的域名进行解析。`ResolveAddress`函数就是对域名进行解析。这个函数直接调用`boost::asio::ip::tcp::resolver`类来实现对域名的解析。其核心代码如下：
```C++
    tcp::resolver resolver(io_service);
    boost::system::error_code ec;
    tcp::resolver::iterator it = resolver.resolve(tcp::resolver::query(host, svc), ec), end;
    if (it != end)
    {
        *endpoint = it->endpoint();
        return true;
    }
```
完成了域名解析之后就开始创建`RpcListener`对象，这个对象主要用于处理TCP的监听，同时将几个事件使用`boost::bind`函数进行注册，这个类是TCP处理处理重点。最后创建`TimerWorker`定时器用于处理定时任务。至此，server开始了运行。

有几个可以值得学习的地方：
* 采用ScopedLocker来对锁进行Lock Guard，成功使用对象的生存时间来控制锁的Lock和UnLock (妈妈再也不担心我忘了释放锁了
* 当需要注册一个对象函数回调的时候，使用`boost::bind()`来进行注册，避免了传递this指针的问题。`shared_from_this()`能够完全避免指针被释放问题，当然，对应的类需要继承`public boost::enable_shared_from_this<T>`。
```C++
boost::bind(&RpcServerImpl::OnCreated, shared_from_this(), _1));
```

## RpcListener类
`RpcListener`主要用于处理TCP Listener请求，主要还是围绕着`boost::asio::ip::tcp::acceptor`进行封装使用。使用前首先要注册几个回调函数。
```C++
   // Set the callback funtion when created a new connection.
    template <typename T>
    void set_create_callback(const T& create_callback)
    {
        _create_callback = create_callback;
    }

    // Set the callback funtion when accepted a new connection.
    template <typename T>
    void set_accept_callback(const T& accept_callback)
    {
        _accept_callback = accept_callback;
    }

    // Set the callback funtion when failed to accept connection.
    template <typename T>
    void set_accept_fail_callback(const T& accept_fail_callback)
    {
        _accept_fail_callback = accept_fail_callback;
    }
```
注册完成之后，`start_listen()`函数开始了监听端口。

`start_listen()`对socket接口进行了一些设置。

```c++
int ret = fcntl(_acceptor.native(), F_SETFD, 
                        fcntl(_acceptor.native(), F_GETFD) | FD_CLOEXEC);
_acceptor.set_option(tcp::acceptor::reuse_address(true), ec);
```
在设置了`FD_CLOEXEC`标志位和`reuse_address`后，就开始`bind`以及注册异步的回掉函数。注册异步的回调函数`async_accept`如下所示。
```c++
 void async_accept()
{
        RpcServerStreamPtr stream(new RpcServerStream(_io_service_pool->GetIOService()));

        if (_create_callback)
            _create_callback(stream);

        _acceptor.async_accept(stream->socket(), boost::bind(
                    &RpcListener::on_accept, shared_from_this(), stream, _1));
}
```

`async_accpet`在每一次都创建一个`RpcServerStream`类作为参数。由于`boost::async_accept`每一次都是要重新注册的，所以在`RpcListener::on_accept`中又再次重新调用`async_accept`中。上文提到的`set_create_callback`注册函数，就是再每一次`async_accept`函数调用时被调用的。即当每创建一个`RpcServerStream`时调用。


`RpcServerStream`用来处理socket的数据连接，这个类下次再捋。





