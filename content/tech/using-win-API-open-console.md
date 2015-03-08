+++
Description = "使用windows API打开控制台"
Tags = ["windows","API","控制台"]
date = "2012-11-24T14:08:07+08:00"
ds_title = "使用WindowsAPI函数打开console控制台"
title = "使用WindowsAPI函数打开console控制台"

+++

s窗口，实时显示一些运行信息，这里就告诉大家是如何实现的，我们做个简单的，其实对控制台的操作还有很多，有兴趣的可以去查资料。

用到的API函数如下：

//创建控制台

AllocConsole;

//获取控制台窗口
GetStdHandle;

//向控制台输出信息
WriteConsole;

//释放控制台
FreeConsole;

具体示例如下:
```C
#include<stdio.h>
HANDLE Handle;
char buff[256];
unsigned long num;
int winmain(){
AllocConsole();
Handle=GetstdHandle();
strcpy(buff,"HELLO WORLD");
WriteConsole(Handle,buff,strlen(buff),&amp;num,NULL);
FreeConsole();
}
```

buff是实际要写入的东西，很简单，调试的时候很适合这样做，既可以看到执行信息，还可以利用它进行多线程编程的调试，用于分析结果。
