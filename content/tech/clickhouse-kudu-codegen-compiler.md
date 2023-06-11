+++
Description = "ClickHouse Kudu存储CodeGen编译"
Tags = ["ClickHouse", "Kudu", "Codegen" ]
title = "在ClickHouse使用Kudu codegen遇到的编译问题"
date = "2023-06-11T23:13:43+08:00"
+++


[Apache Kudu - Fast Analytics on Fast Data](https://kudu.apache.org/)是一个优秀的列式存储系统，现阶段我们把Kudu存储对接在Clickhouse上作为一个存储引擎。本文首先介绍了ClickHouse在编译的时候怎么实现降低二进制对操作系统的依赖的，然后讲述了Kudu如何使用LLVM来实现Codegen功能，最后介绍了编译和运行的时候遇到的一些编译上的坑以及方案。

<!--more-->

# ClickHouse在编译时的一些工作
ClickHouse为了降低自己对操作系统的依赖，做了以下功能：
* 所有依赖库的代码全部静态编译
* 将libcxx/libcxx-abi 静态编译，不依赖系统
* 将libc中一些高版本的函数实现用musl替代，从而使高版本编译的ClickHouse可以在低版本libc上运行。

通过命令可以看到，ClickHouse的依赖只有寥寥几个：
```bash
$ ldd programs/clickhouse
        linux-vdso.so.1 (0x00007ffc902aa000)
        /$LIB/libonion.so => /lib64/libonion.so (0x00007ffbf3fda000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007ffbf3b9d000)
        libdl.so.2 => /lib64/libdl.so.2 (0x00007ffbf3999000)
        librt.so.1 => /lib64/librt.so.1 (0x00007ffbf3790000)
        libc.so.6 => /lib64/libc.so.6 (0x00007ffbf33ce000)
        /lib64/ld-linux-x86-64.so.2 (0x00007ffbf3dbd000)
        libm.so.6 => /lib64/libm.so.6 (0x00007ffbf304c000)
```

## 静态编译依赖库
静态编译依赖库这个事情比较麻烦，基本上就是能复用`CMakeLists.txt`就复用，不能复用的就重写，并通过启动的时候判断，是否使用静态库（虽然留了开关，大部分应该都是用静态库的）。
除了搞这个之外，还要梳理静态库依赖关系，确保所有的依赖都在项目里。

## libcxx/libcxx-abi 静态编译和 libc 低版本兼容
当一个C++函数调用的时候，通常调用链路为：
LIBCXX--->LIBC---->syscall---->kernel Function
syscall这部不一定要有，如果只是调用计算函数，没有内核调用的话就不会出现。当有和内核操作例如`printf`的时候，才会有系统调用。
libcxx/libcxx-abi作为C++的基石，需要保证所有编译的文件都使用相同的库进行编译，要不然就会出现诡异的abi问题。
libc作为操作系统的基石，一般来说在高版本上编译的二进制是不能在底版本操作系统上运行的，会出现`libc.so.6: version GLIBC_2.14 not found`这种错误。造成这个问题的原因可以看这篇文章
[How the GNU C Library handles backward compatibility | Red Hat Developer](https://developers.redhat.com/blog/2019/08/01/how-the-gnu-c-library-handles-backward-compatibility)。简单来说GLIBC的函数实现是有多个版本的，在编译时会将使用的版本号链接进去，当使用低版本运行时，会找不到高版本的符号。  
有两个方案可以选择，一个是link的时候选择指定libc函数的版本，另外一个就是直接实现它。
方案一要找到所有调用的地方，过于复杂，所以采用方案二，在link的时候直接把对应的实现链接进去，这样就解决了这个问题。
在选择实现的版本时，ClickHouse选择了musl这个系统库，musl作为libc的轻量级版本，体积小，多见于路由器等小内存系统，部分兼容libc。ClickHouse从musl抽取了部分函数的代码实现，当调用高版本函数时，就用这里的实现进行链接，这样就不会有glibc兼容性问题，还保证了数学计算的准确性。具体实现可以参考这个[ClickHouse/base/glibc-compatibility at master · ClickHouse/ClickHouse · GitHub](https://github.com/ClickHouse/ClickHouse/tree/master/base/glibc-compatibility)
ClickHouse甚至重写了最重的memcpy函数，替换成AVX版本的实现，从而提高效率。

但是为了确保链接器不会提前将函数选择glibc中的版本进行链接，在我们链接`glibc-compatibilty`之前，是不能出现`-lm -lc`这样的动态链接库的。libm提前出现会导致链接器不会使用我们定义的函数。
https://git.woa.com/Khaos/CHAI_CORE/llvm/commit/1748849ff5e95ed4352ba0deaa98186293d57e7f
这个提交就是用来做这个事情的，ClickHouse也做了相同的处理。

总体说来，为了实现上述的逻辑，
* 编译阶段：
将libcxx/libcxx-abi的include文件夹放到编译参数末尾，新增`-nostdinc++`参数，避免编译器使用系统的libcxx
* link阶段，将这三个库放到一个start-group中链接，并且加上`-nodefaultlibs`从而实现链接
```
-Wl,--start-group  base/glibc-compatibility/libglibc-compatibility.a  contrib/libcxx-cmake/libcxx.a  contrib/libcxxabi-cmake/libcxxabi.a  contrib/libunwind-cmake/libunwind.a  -Wl,--end-group  -nodefaultlibs
```

# Apache Kudu codegen迁移和编译流程
## Apache Kudu codegen流程
Kudu在实现codegen的代码位于： [kudu/src/kudu/codegen at master · apache/kudu · GitHub](https://github.com/apache/kudu/tree/master/src/kudu/codegen)，其编译流程如下：
* 编译阶段使用`clang++ --emit-llvm precompiled.cc -o precompiled.ll -Ixxxx -Dxxxx`命令，将`precompiled.cc`中涉及到的函数编译成`precompiled.ll`文件，里面包含`precompiled.cc`中的函数全部编译成IR指令
* 将`precompiled.ll`的内容通过脚本写入到`precompiled.ll.cc`的`precompiled_ll_data`变量中。
* 程序运行时，调用`llvm::ParseIR`解析`precompiled_ll_data`变量的IR指令，生成Module，然后按照表结构，调用`MakeProjection`函数，在这个函数里调用Module里的函数拼接出一个函数，并进行编译后获得一个函数指针。
* 使用这个函数指针调用函数即可执行codegen代码

## 遇到的问题
### Parse IR failed
调查发现，ClickHouse 代码库中使用的LLVM版本是9.0.0，我们clang++编译器使用的版本是11.1.0，11.1.0生成的IR不支持使用LLVM 9.0.0解析导致的。
解决方案： 升级库中LLVM版本到11.1.0

### codegen动态生成代码阶段时报错
codegen动态生成代码的时候一直报`dlopen() not supported on this platform`，理论上`dlopen`是支持Linux，为什么会出现这个问题。
通过调查后发现，cURL 库中的一个CMAKE变量和LLVM库中的变量冲突了，导致LLVM检测不到dlopen函数，这个改动修改了这个问题，对这个变量进行重命名用来修复。

### codegen 阶段core
在codegen阶段的时候，发生了一个assert，且在llvm里assert的，真是百思不得其解。
aseert代码：
[llvm-project/Verifier.cpp at llvmorg-11.1.0](https://github.com/llvm/llvm-project/blob/llvmorg-11.1.0/llvm/lib/IR/Verifier.cpp#L2261)

通过排查发现，我们函数设置的Function和设置的属性长度不一致，导致检查不过。

问题代码：
```
  // Note that these arguments are 1-based indexes.
  f->addParamAttr(1, llvm::Attribute::NoAlias);
  f->addParamAttr(2, llvm::Attribute::NoAlias);
  f->addParamAttr(3, llvm::Attribute::NoAlias);
```
虽然文档里写的是1-based indexes，但实际上是0-based indexes

还有几个问题，为什么相同代码kudu是正常的？研究了一下llvm IR的检查，IR检查在Debug版本下会新增很多检查，Kudu编译的llvm是release版本的，就会加入这个参数的检查，导致出现问题。
同样我们编译clickhouse时用release版本，也是正常的。
这里给kudu提了一个PR，不知道何时能够合并。

### 执行生成的代码出现CoreDump
本来吃着火锅唱着歌以为迁移就搞好了，结果遇到了迁移路上最大的障碍，打开了codegen的代码必Core，且没有core栈。这就傻眼了，这代码没办法调试，也没有调试信息，只能看汇编，汇编代码如下：
```
JIT(0x7ffebe805530)`kudu::BasicCopyCell:
    ...
    0x7ffff7fdd316 <+150>: movabsq $0x0, %rax
    0x7ffff7fdd320 <+160>: callq  *%rax
->  0x7ffff7fdd322 <+162>: movb   $0x1, 0x37(%rsp)
    ...
    0x7ffff7fdd334 <+180>: retq 
```
我们将函数`inline`这些全部关掉，发现这里的调用对应的是IR代码里的extern函数，即代码里只有申明没有定义的函数。从这里我们判断，llvm的codegen找不到这些函数地址，就将这些函数地址变为0x0，然后使用`callq`调用0x0自然就失败了。

接下来就是要调查为什么llvm的ExecutionEngine为什么拿不到这些函数地址？ClickHouse和Kudu是怎么做到的？在对比了ClickHouse的CHJIT.cpp和Kudu的流程，以及对llvm加代码后发现，llvm获取函数的流程如下：
* 调用`dlopen(nullptr)`将二进制加载进系统
* 使用`dlsym()`获取符号对应的函数地址

经过排查发现，在[llvm-project/JITSymbol.cpp at llvmorg-11.1.0 · llvm/llvm-project · GitHub](https://github.com/llvm/llvm-project/blob/llvmorg-11.1.0/llvm/lib/ExecutionEngine/RuntimeDyld/JITSymbol.cpp#L108)
此处在查找函数地址的时候，当找不到符号表的时候，就会报错，不会对后续的函数进行查找。
kudu本身的代码也没有处理这种错误，也没有检查，就一路放过去执行了。

好，接下来就是看为什么找不到函数表了。通过比对，排除了demangle的时候名字不一致出现的问题，那就来看具体的函数找不到了。

#### Glog的函数找不到
首当其冲的是找不到glog的函数，因为`precompiled.cc`中有一些glog的函数，
`google::base::CheckOpMessageBuilder::CheckOpMessageBuilder(char const*)`找不到定义。
很奇怪，通过命令发现，函数被隐藏了。
```
$ objdump -t clickhouse|grep _ZN6google4base21CheckOpMessageBuilderC1EPKc
0000000015598900 l     F .text  00000000000001e9              .hidden _ZN6google4base21CheckOpMessageBuilderC1EPKc
```

经过查找一番，glog把这些函数隐藏了，kudu对glog打了一个patch，详见：
[kudu/glog-make-internals-visible.patch at master · apache/kudu · GitHub](https://github.com/apache/kudu/blob/master/thirdparty/patches/glog-make-internals-visible.patch)
打上这个patch之后，glog类的函数就再也不会报错了。

#### cxx::abi11类型的函数
某些使用了string的函数依然找不到，函数签名如下：`google::base::CheckOpMessageBuilder::NewString[abi:cxx11]()`，很奇怪的一个名字。
经过排查发现我们编译的时候没有使用ClickHouse提供的libcxx，而是clang默认的libcxx，这样就会出现函数abi申明不兼容的问题。

重新生成了IR文件，在生成的时候Include了libcxx的header，此处的ABI文件就一致了。

#### libcxx类型的函数（目前尚未解决）
当我们在编译debug版本的时候，会出现libcxx中的函数找不到`std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::__init(unsigned long, char)`，通过命令发现：
```bash
$ objdump -t clickhouse|grep _ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEE6__initEmc
0000000015598900 l     F .text  00000000000001e9              .hidden _ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEE6__initEmc
0000000029dad050 l     F .text  0000000000000161              _ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEE6__initEmc
0000000029db9c00 l     F .text  0000000000000164              _ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEE6__initEmc
0000000029dbeb40 l     F .text  0000000000000161              _ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEE6__initEmc
0000000029dcc2e0 l     F .text  0000000000000161              _ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEE6__initEmc
```
有多个函数定义，但是第一个是`.hidden`的，导致找不到函数地址。但是在release的时候就能正确找到这个函数地址，且不是debug版本。
考虑了再三，这个问题排查下去收益不高。libcxx是由glog引入的，就在`precompiled.cc` 使用的代码中，把GLOG相关的代码注释掉。这样就不会出现libcxx的函数，后续所有函数能找到正确的地址，并且在kudu编译的时候检查错误，避免重复出现问题。

这里引入了一个风险点，在debug版本如果新加了代码有可能出现找不到的情况，目前暂时看起来还可控，所以就忽略了。

# 结果
目前ClickHouse中的kudu已经能够完整的实现codegen，代码也不再coredump了。本身这个问题很有意思，花了一个星期来看这个问题，值得写个文章记录一下。本文也主要讲述了本次编译修改的各个部分。

