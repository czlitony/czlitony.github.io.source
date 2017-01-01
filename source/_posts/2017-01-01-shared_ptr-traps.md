---
title: shared_ptr陷阱
categories : C++
tags : [shared_ptr,C++11]
date : 2017-01-01 14:02:30
---

本文主要介绍C++11中的shared_ptr在使用的过程中容易出现的错误用法。

<!-- more -->

## 不要把一个原生指针给多个shared_ptr管理
```
int* ptr = new int();
std::shared_ptr<int> sp1(ptr);
std::shared_ptr<int> sp2(ptr);
```
ptr对象被析构了2次

## 不要把this指针赋值给shared_ptr
```
class Test {
public:
    void do_something() {
        m_sp = std::shared_ptr<Test>(this);
    };  
private:
    std::shared_ptr<Test> m_sp;
};

Test* t = new Test();
std::shared_ptr<Test> local_sp(t);
local_sp->do_something();
```
t被析构了两次!!
被shared_ptr管理的对象在内部需要传递this指针的时候需要采用如下的方式：
```
class Test : public std::enable_shared_from_this<Test> {
public:
    void do_something() {
        m_sp = std::shared_ptr<Test>(shared_from_this());
    };  
private:
    std::shared_ptr<Test> m_sp;
};
```

## shared_ptr作为被保护的对象的成员时，小心因循环引用造成无法释放资源
```
class B;
class A {
public:
    std::shared_ptr<B> m_b;
};
 
class B {
public:
    std::shared_ptr<A> m_a;
};

void test3() {
    while (true)
    {
        std::shared_ptr<A> a(new A);
        std::shared_ptr<B> b(new B);
        a->m_b = b;
        b->m_a = a;
    }
}
```
两个shared_ptr对象互相引用的时候容易导致内存无法释放，解决措施为其中一个用weak_ptr：
```
class B;
class A {
public:
    std::weak_ptr<B> m_b;
};
 
class B {
public:
    std::shared_ptr<A> m_a;
};
```

## 不要在函数实参里创建shared_ptr
下面的代码有内存泄漏的危险
```
// 声明
void f(A *p1, B *p2);
// 使用
f(new A, new B);
```
假如new A先于new B发生(因为C++的函数参数的计算顺序是不确定的)，那么如果new B抛出异常，那么new A分配的内存将会发生泄漏。
学过shared_ptr之后我们或许会尝试如下方法"解决"该问题：
```
// 声明
void f(shared_ptr<A> p1, shared_ptr<B> p2);
// 使用
f(shared_ptr<A>(new A), shared_ptr<B>(new B));
```
可惜，这么写依然有可能发生内存泄漏，因为两个shared_ptr的构造有可能发生在new A与new B之后。

若这么调用f：
```
f(make_shared<A>(), make_shared<B>());
```
那么就不可能发生内存泄漏了，与上面用new来初始化的情形对比，make_shared保证了第二new发生的时候，第一个new所分配的资源已经被shared_ptr管理起来了，故在异常发生时，能正确释放资源。

一句话建议：总是使用make_shared来生成shared_ptr！

另外的例子：
function(shared_ptr<int>(new int), g());
可能的过程是先 new int, 然后调 g(), g()发生异常, shared_ptr<int> 没有创建, int内存泄露
推荐写法:
shared_ptr<int> p(new int());
f(p, g());

## weak_ptr在使用前需要检查合法性
```
std::weak_ptr<int> wp;
{
    std::shared_ptr<int> sp(new int(1)); //sp.use_count()==1
    wp = sp; //wp不会改变引用计数，所以sp.use_count()==1
    std::shared_ptr<int> sp_ok = wp.lock(); //wp没有重载->操作符。只能这样取所指向的对象
}
std::shared_ptr<int> sp_null = wp.lock(); //sp_null.use_count()==0;
```
因为上述代码中sp和sp_ok离开了作用域，其容纳的K对象已经被释放了。
得到了一个容纳NULL指针的sp_null对象。在使用wp前需要调用wp.expired()函数判断一下。
因为wp还仍旧存在，虽然引用计数等于0，仍有某处“全局”性的存储块保存着这个计数信息。
直到最后一个weak_ptr对象被析构，这块“堆”存储块才能被回收。否则weak_ptr无法直到自己
所容纳的那个指针资源的当前状态。

## 不要new shared_ptr<T>
本来shared_ptr就是为了管理指针资源的，不要又引入一个需要管理的指针资源shared_ptr<T>*

## 尽量不要get
```
class Base {
public:
    virtual ~Base() {};
};
class Derived : public Base {
};

std::shared_ptr<Base> sp(new Derived); //通过隐式转换，储存D的指针。
Base* b = sp.get(); //shared_ptr辛辛苦苦隐藏的原生指针就这么被刨出来了。
Derived* d = dynamic_cast<Derived*>(b); //这是使用get的正当理由吗
```
正确的做法：
```
    std::shared_ptr<Base> spb(new Derived) ;
    std::shared_ptr<Derived> spd = std::dynamic_pointer_cast<Derived>(spb); //变成子类的指针
```

另一个同get相关的错误
```
shared_ptr<T> sp(new T);
shared_ptr<T> sp2(sp.get());//指针会析构2次而错误
```