+++
date = "2017-10-15T18:14:52+08:00"
title = "C++指向成员函数指针"
Tags = ["C++", "point"]
+++

C++的语法总是会隐藏在一些边角上的语法，让你觉得还能这么干。最近在了解boost::bind的时候发现了这个语法，之前都不了解这个语法的。拿出来介绍一下，也算记录一下。这个语法在boost::bind还有Inside C++ Object Model中都有用到。
代码和表达均来自与ref[1]。


## 成员函数指针

废话不多说，上代码：

```C++
class Foo {
public:
    int f(string str) {
        cout<<"Foo::f()"<<endl;
        return 1;
    }
};

int main() {
    int (Foo::*fptr)(string) = &Foo::f;
    Foo obj;
    (obj.*fptr)("str");
    Foo *p = &obj;
    (p->*fptr)("str");
}
```

如上代码所示， `int (Foo::*fptr)(string)`定义了一个Foo类中的一个成员函数指针，其指向了Foo::f函数。相比起普通函数存放的是函数所指向的地址，其存放的是这个函数在类中相对的offset，这样只要是有相同的内存offset的均能互相转换。
所以要调用它，必须绑定在对应的class object的地址上，才能调用该函数。


<!--more-->

### 成员函数指针转换规则

成员函数指针是有一定的转换规则时，当成员中不存在virtual函数时，那么具体调用函数是在编译期就决定了，否则只会在运行态决定。

* 非虚函数

```C++
class Foo {
public:
    int f(char *c = 0) {
        std::cout << "Foo::f()" << std::endl;
        return 1;
    }
};

class Bar {
public:
    void b(int i = 0) {
        std::cout << "Bar::b()" << std::endl;
    }
};

class FooDerived : public Foo {
public:
    int f(char *c = 0) {
        std::cout << "FooDerived::f()" << std::endl;
        return 1;
    }
};

int main(int argc, char *argv[]) {
    typedef int (Foo::*FPTR) (char*);
    typedef void (Bar::*BPTR) (int);
    typedef int (FooDerived::*FDPTR) (char*);

    FPTR fptr = &Foo::f;
    BPTR bptr = &Bar::b;
    FDPTR fdptr = &FooDerived::f;

    // bptr = static_cast<void(Bar::*)(int)>(fptr); // 错误
    fdptr = static_cast<int(Foo::*)(char*)>(fptr); // 正确，逆变性规则

    Bar obj;
    ( obj.*(BPTR) fptr )(1); // 调用 Foo::f()
}
```
当把`fptr`转换为`bptr`时，因为函数的返回值类型以及类名是不一样的，所以这时候转换是失败的。所以调用第一个`static_cast`将会获得编译器的报警。

而第二个对fptr的调用，即使我们强制转换成了BPTR，实际上还是调用了Foo::F。这个绑定在编译阶段，被编译器优化成了：
```C++
fptr(&obj, 1);  //隐藏的this指针
```
由于我们在函数中没有用到this指针，所以函数可以被正常执行。但这是一个未定义行为。详见ref[2] P175

* 虚函数

当一个类中有了虚函数时，所有的行为都只能在运行时决定。具体代码如下：
```C++
class Foo {
public:
    virtual int f(char *c = 0) {
        std::cout << "Foo::f()" << std::endl;
        return 1;
    }
};

class Bar {
public:
    virtual void b(int i = 0) {
        std::cout << "Bar::b()" << std::endl;
    }
};

class FooDerived : public Foo {
public:
    int f(char *c = 0) {
        std::cout << "FooDerived::f()" << std::endl;
        return 1;
    }
};

int main(int argc, char *argv[]) {
    typedef int (Foo::*FPTR) (char*);
    typedef void (Bar::*BPTR) (int);

    FPTR fptr = &Foo::f;
    BPTR bptr = &Bar::b;

    FooDerived objDer;
    (objDer.*fptr)(0); // 调用 FooDerived::f()，而不是 Foo::f()

    Bar obj;
    ( obj.*(BPTR) fptr )(1);// 调用 Bar::b()，而不是 Foo::f()
}

// Output:
// FooDerived::f()
// Bar::b()
```

当有虚函数时，编译器会把上面的调用转化为一个编译时期的式子：
```C++
//C++伪代码，vptr为虚表
* objDer.vptr[(int)fptr](0);
```

这样，`fptr`存储着对应的虚函数表中的offset，调用方式就和调用虚函数一致了。这时候`fptr`存放的就是对应的函数在虚函数表中的index。
同理，另外一个在调用的时候，被强制转换成了如下C++伪码：
```C++
//C++伪代码
* obj.vptr[(int) fptr](1);
```
这样调用成功只是因为“一个指向虚成员的指针能在不同地址空间之间传递，只要二者使用的对象布局一样”。（这里一样指的就是虚函数值）

如果我把上述`class Bar`的定义变为
```C++
class Bar {
public:
    virtual void a(int i=0){
        std::cout<<"call Bar::a()" << std::endl;}
    virtual void b(int i = 0) {
        std::cout << "Bar::b()" << std::endl;
    }
};
```
上述程序在OSX下调用了`Bar::a`，这个行为是一个未定义行为。

* 其他

还有多重继承下的行为，具体可以查看ref[2] 第4章 function语义学进行参考。


## Boost::bin绑定对象成员函数的简单实现

在Boost::bind中，可以对对象的成员函数，就使用的是指向对象成员函数的指针。代码源自ref[3].

我们首先定义一个占位符
```C++
namespace
{
	class placeholder_ {};
	placeholder_ _1;
}
```

调用是不接收参数的bind类实现：

```C++
template <typename R, typename T, typename Arg>
class simple_bind_t
{
private:
	typedef R (T::*F)(Arg);
	F f_;
	T* t_;
	Arg& a_;

public:
	simple_bind_t(F f, T* t, Arg &a)
		: f_(f), t_(t), a_(a)
	{}

	R operator()() // 无参数，参数在bind时确实，并保存在a_，调用时就不需要传入参数了。
	{
		return (t_->*f_)(a_);
	}
};
```
通过`typedef`定义了一个`F`的成员函数指针，类为T，参数为Arg，返回值为R。在调用时使用的是`(t_->*f)(a_)`来进行调用。

同理实现一个只带一个参数调用时候的类：
```C++
template <typename R, typename T, typename Arg>
class simple_bind_t2
{
private:
	typedef R (T::*F)(Arg);
	F f_;
	T* t_;

public:
	simple_bind_t2(F f, T* t)
		: f_(f), t_(t)
	{}

	R operator()(Arg& a)
	{
		return (t_->*f_)(a);
	}
};
```
下面统一用户调用的接口

```C++
template <typename R, typename T, typename Arg>
simple_bind_t<R, T, Arg> simple_bind(R (T::*f)(Arg), T* t, Arg& a)
{
	return simple_bind_t<R, T, Arg>(f, t, a);
}


template <typename R, typename T, typename Arg>
simple_bind_t2<R, T, Arg> simple_bind(R (T::*f)(Arg), T* t, placeholder_& a)
{
	return simple_bind_t2<R, T, Arg>(f, t);
}
```

最后是调用函数：

```C++
class bind_test
{
public:
	void print_string(const std::string str)
	{
		printf("%s", str.c_str());
	}
};

void test()
{
	bind_test t;
	std::string h = "hehe\n";

	simple_bind(&bind_test::print_string, &t, h)();

	simple_bind(&bind_test::print_string, &t, _1)(h);

	// 还可以使用boost.function保存我们自己实现的simple_bind绑定后的结果，和
	// 使用boost.function保存boost.bind完全兼容。
	// 由此，大家可以想象一下boost.function的实现原理。
	boost::function<void (const std::string)> f;
	f = simple_bind(&bind_test::print_string, &t, _1);
	f(h);
}
```


## 总结
之前用Boost::bind的时候总觉得不太明白，特别是对象的声明周期比较疑惑。现在了解了从这个指针了解了bind的一些简单实现，从而进一步理解了bind对象的生命周期，用起来更加放心了呢。

## 参考
*  [C++教程：指向成员函数的指针](https://kelvinh.github.io/blog/2014/03/27/cpp-tutorial-pointer-to-member-function/)

* [《深度探索C++对象模型》](https://item.jd.com/10909788.html)

* [Boost.bind和占位符的实现原理](https://bbs.avplayer.org/t/boost-bind/232)