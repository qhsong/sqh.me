+++
Description = "pcap man 1.6中文翻译"
Tags = ["pcap","man",]
date = "2015-05-13T14:07:01+08:00"
title = "pcap 1.6 man手册"

+++
pcap 1.6主要的函数说明指南，自己翻译了一下，贴出来共享：  

## 打开一个可以进行读取的设备
### 打开设备  
* live capture  
	 - 使用`pcap_create()`创建设备，并且使用`pcap_activate()`激活。  
	 - 得到设备的list，使用`pcap_findalldevs()`，释放的到的list，使用`pcap_findalldevs()`  `pcap_lookupdev()` 得到第一个不是*loopback*的设备  
* 从文件中获得  
	 - 打开一个已经保存好的文件，使用`pcap_open_offline()`直接输入路径,`pcap_fopen_offline()`输入的是`FILE *`类型  
	 - 得到一个`FILE *`类型，使用`pcap_file()`函数  
* **fake** pcap_t 作为  
	- `pcap_open_dead()` 生成一个无用句柄，仅作为写入文件和编译过滤表达式  

上述函数返回一个指向`pcap_t`类型的指针作为抓包的句柄，要关闭这个句柄，使用`pcap_close()`

### 句柄参数设置
* snapshot length
	抓取数据包长度。默认抓取的是整个数据包，这将花费更多的CPU时间、磁盘空间将数据包拷贝到程序中。如果只需要部分的数据包长度，那可以设置这个数值为需要的数据包长度即可。
	如果这个长度设置和*snaplen*相同，并且*snaplen*小于抓取的数据包的长度，那么第一个和*snaplen*长度相同的数据包将会被捕获。
	65535是最大值
	使用`pcap_set_snaplen()`来设置
* premiscuous mode
	混杂模式，不用多说
	使用`pcap_set_promisc()`设置
* monitor mode
	IEEE 802.11 wireless协议下，即时打开混杂模式也收不到别人的包。monitor model 又叫 rfmon mode（radio frequency monitor）。在这个模式下，网卡会提供802.11包头，也有可能会提供有关这个帧的伪包头。在这个模式下，网卡可能会和原有网络断开，所以这时候网卡使用不了任何无线网络。  
	设置monitor mode使用`pcap_set_rfmon()`函数，检测是否能设置这个模式，使用`pcap_can_set_rfmon()`  
* read timeout
	通常而言，数据包一旦到达，抓包程序就会被唤醒。这样每抓一次包，就会产生一个或多个系统调用。  
	但是，我们可以指定当数据包到达时，中间有一个短的延迟再进行抓包和包处理。这样在这个数据包被抓包程序捕获时，会累计了不止一个包。那么，一次程序的唤醒就能处理多个包，那么一系列的系统调用就能处理多个数据包，而不是一个包。这样极大的减少了CPU占用率。  
	这个数值是必须设置的，这样程序就不用等待操作系统抓包缓冲区填满了再给抓包程序，如果数据包到达的比较慢，那将会等待很长一段时间。  
	并不是所有的操作系统都支持这个数值，在不支持的操作系统上，这个时间是被忽略的。这个timeout设置为0，那么将会导致读取包函数无延迟的等待足够多的数据包到达。  
	**NOTICE**：read timeout值不能被用于读取一个包并在指定的一段时间内返回，因为在某些平台上，timeout并不是被支持的，在其他平台上，直到第一个包到达时，定时器才会开始计时。这就意味着read timeout是无效的，例如，在一个程序要允许包抓取的loop去周期的poll用户输入，那么这并不保证在如果没有数据包到达的情况下，即时timeout过期了，读取数据包的函数调用会返回。（PS:-1表示无延迟）  
	使用`pcap_set_timeout()`来设置read timeout
* buffer size
	缓存buffer大小，如果buffer满了，多余部分就要丢掉，除非你的操作系统是非页式存储的操作系统（non-pageable operating system memory）。使用`pcap_set_buffer_size()`设置
* timestamp type
	时间戳格式，目前用不到
	
 使用`pcap_datalink()`函数来决定数据链路层的类型，以此来确定包头的格式。返回值在http://www.tcpdump.org/linktypes.html
 不要假定从一个给定的抓取或者文件中获得的数据包会有任何给定的数据链路层头。举个栗子，Linux上的'any'类型的设备在数据链路层上使用DLT_LINUX_SLL 就算在这个系统上的所有设备在这个'any'设备被打开的时候，仍然是其他数据类型，如DLT_EN10MB （以太网）。

### 选择一个数据链路层头来进行living capture
 有一些设备不止支持一种数据类型，为了得到网卡支持的链路层头部，使用`pcap_list_datalinks()`在一个已经激活的`pcap_t`上获得数据类型。释放链表使用`pcap_free_datalinks()`。设置抓取的数据包头部`pcap_set_datalink()`。这个操作应该在设备已经激活之后，和在设备没有激活而且没有任何过滤器被编译安装之前。

## 读取数据包
数据包的读取使用`pcap_dispatch()`或者`pcap_loop()`，可以调用回调函数来处理一个或者多个数据包。或者使用`pcap_next()` `pcap_next_ex()`来返回下一个数据包。  
回调函数提供了一个指向`struct pcap_pkthdr`的指针，这个数据结构里面包含 ts(timeval 时间戳),caplen(可用的长度),len(数据包总长，可能比caplen长)  
回调函数中也提供了一个指向`const u_char`类型的指针，指向了caplen长度的数据包。参见**snapshot length**  
 
`pcap_next()`传递一个指向`struct pcap_pkthdr`的指针作为参数，并将其填充上时间戳和数据包长度。这个函数调用成功，返回一个`const u_char *`，指向数据包
`pcap_next_ex()`传递两个参数，一个是`struct pcap_pkthdr *`,另外一个是`const u_char *`

结束`pcap_dispatch()`或者`pcap_loop` ,使用`pcap_breakloop()`

如果没有数据包到达，默认情况下，`pcap_dispatch()`,`pcap_next()`和`pcap_next_ex()`将会阻塞。在某些平台上，如果指定了read timeout，那么等待将会在read timeout过期后停止。应用程序应该在这个上准备，因为他可能发生在一些平台上，但不能依赖这个数值。

句柄可以被设置为非阻塞模式，和阻塞模式不同，这程序将会返回一个没有可用来读取的数据包的指示。使用`pcap_setnonblck()`来进入非阻塞模式，使用`pcap_getnonblock()`函数来获得一个句柄是不是非阻塞模式。在Mac OS X 10.6上，非阻塞模式不能正常运行。

非阻塞模式通常将程序和`select()` 、`poll()`或者其他平台上等待一系列文件描述符准备好可读的函数结合。通常获得可以读的句柄，使用`pcap_get_selectable_fd()`。如果没有描述符可读，则返回-1 。另外，由于多种原因，一个或者多个这样的历程可能不能正常工作，`pcap_get_selectable_fd()`文档给出了细节。注意，如果read timeout过期了，那么从`pcap_t`中读取数据包的操作将不会返回任何值。类似的，在`select()`、`poll()`中，如果read timeout过期，那意味着一个文件描述符可读了，尽管并没有任何数据包可以读取。

## 过滤器
只抓需要的包。在live capture模式下，过滤器在内核状态下执行，这也避免了把无关的包从内核状态拷贝到用户态。  
过滤器就是一串字符串，语法和语意都被pcap-filter所描述。过滤器首先被`pcap_compile()`编译成了一个伪机器语言，然后使用`pcap_setfilter()`来设置过滤器到句柄中。`pcap_compile()`的结果需要使用`pcap_freecode()`来进行释放。对于过滤的字符串，`pcap_compiler()`函数可能需要一个网络掩码，这个掩码可以由`pcap_lookupnet()`函数获取。同时，这个函数还能获取网络的地址和子网掩码在给定的装置上。  
使用`pcap_offline_filter()`,那么，编译好的过滤器也能直接被应用在一个已经被读取的包上面。  

## 抓取包方向设置
默认的，libpcap会抓取所有方向的包，为了限制抓取方向，使用`pcap_setdirection()`函数

## 抓取统计
`pcap_stats()`

## 打开一个句柄作为写入文件
略

## 写入一个数据包
略

## 注入一个数据包
当你有足够的权限时，你可以在live capture里注入一个数据包在网络上，使用`pcap_inject()` 或者`pcap_sendpacket()` 
man pcap_inject()
```C
	int pcap_inject(pcap_t *p, const void *buf, size_t size);
	int pcap_sendpacket(pcap_t *p, const u_char *buf, int size);
```
`pcap_inject()` 发送一个原始的数据包在网络接口上，buf指向数据包（包括链路层），size参数是数据包长度  

**NOTE** 即使成功打开了网络接口，你也可能没有权限发送数据包，或者可能并不支持发送数据包。就像`pcap_open_live()`没有标志位去指示是否能够开放一个抓包、发包或者抓包发包操作，你不能够请求一个已经打开的网络端口支持发送，并且在打开设备的时候决定发送数据包是可能的。一些设备同时也有可能不支持发送数据包。  
**Note2** 在一些平台上，已经发送的数据包的数据链路层包头可能不会和你提供的包头一样。例如源目的地址，如果相应的平台不支持发送原始未经修改的数据包，那么当头部包含这样的一个地址，很有可能被改变成真正的发送的网卡的物理地址。  
`pcap_sendpacket()`和`pcap_inject()`类似，只是在发送成功时，它的返回值为0，而不是返回写了多少数据包长度。  

## 报告错误
`pcap_statustostr()`

## 得到library版本
`pcap_lib_version()`

## 后向兼容性
略

