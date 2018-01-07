+++
date = "2018-01-07T18:36:52+08:00"
title = "两个C++小问题汇总"
Description = "c++ 使用问题 汇总"
Tags = ["c++", "stl", "UB"]
+++

本文对近期使用C++的两个问题进行汇总。
<!--more-->
## `static_cast` enum 到未定义的值
[https://stackoverflow.com/questions/18195312/what-happens-if-you-static-cast-invalid-value-to-enum-class](https://stackoverflow.com/questions/18195312/what-happens-if-you-static-cast-invalid-value-to-enum-class)

对于这个问题，上面的回答解释了这个问题。显然这是一个未定义行为，以后在使用时要注意。

如果将一个外部输入的数字直接cast到enum上，那么这就是不可控的行为。 这的确是我思考的不对

## `vector::reserve()`的相关用法

为了实现C++11中的`shrink_to_fit`函数，我们可以借助`reserve`来实现对空间的释放。

这里有一个比较坑的点在于，`reserve`函数只处理n大于当前cap的情况，如果n小于当前的cap，那么这个函数是不做任何行为的。

所以如下代码`reserve`函数基本没有进行任何操作，详见[http://www.cplusplus.com/reference/vector/vector/reserve/](http://www.cplusplus.com/reference/vector/vector/reserve/)

```C++
std::vector<int> v(10000);
v.erase(v.begin() + 10, v.end());
v.reserve(10);  //Not working
```
所以即使采用:
```C++
v.reserve(v.size());
```
也是不行的。

一种比较好的方式是使用如下方式来实现`shrink_to_fit`：

```C++
std::vector<int>(v).swap(v);
```

这个实现方式是这样，使用std::vector<int>(v)新建了一个临时对象，他使用了`v.size()`来对这个临时对象进行初始化，然后使用`std::copy`函数来对数据进行拷贝。这样新建的对象就没有任何空间浪费。

然后使用swap交换了内部的指针，这样临时对象中的指针和空间就到了`v`中，`v`原本的数据就在这个临时对象中，并随着生命周期被释放。

然而，这还要复制一遍，不如直接使用`shrink_to_fit`爽。。

其实在某些场景下，`deque`会比`vector`更合适。因为它使用的是分段的内存块，而不是连续的内存块。

## Ref
* [高效使用 C++ 的 vector](http://senlinzhan.github.io/2015/03/31/C-%E4%BD%BF%E7%94%A8vector%E7%9A%84%E6%AD%A3%E7%A1%AE%E5%A7%BF%E5%8A%BF/)
