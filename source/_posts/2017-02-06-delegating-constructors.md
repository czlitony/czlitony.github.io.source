---
title: C++构造函数调用构造函数问题
categories : C++
tags : [delegating constructors,C++11]
date : 2017-02-06 17:53:30
---

C++中在构造函数中调用另外一个构造函数来对成员变量进行初始化是错误的，这种方式无法正常初始化变量

<!-- more -->

直接看例子吧：
```
#include <iostream>

class Test
{
public:
    int m_a;

    Test(int a) : m_a(a) {
    }

    Test() {
        Test(1); //wrong
    }
};

int main(int argc, char* argv[]) {
    Test var;
    std::cout << var.m_a << std::endl;

    system("pause");
    return 0;
}
```
在该例中，试图调用Test(1)来对m_a进行初始化， 这个时候Test(1)产生的是临时对象，其地址和当前的对象不同， 所以想用这种方法来对变量进行初始化会失败， var.m_a是未定义的。

那么怎么初始化呢？
## placement new
它的作用是在已分配的原始内存中初始化一个对象，它与operator new的其他版本不同之处在于它并不分配内存。注意这种方法是危险的做法！
```
class Test
{
public:
    int m_a;

    Test(int a) : m_a(a) {
    }

    Test() {
        new(this) Test(1);
    }
};
```

## delegating constructors
C++11引入了委托构造函数， 这样就可以调用另外的构造函数来进行初始化了。
```
class Test
{
public:
    int m_a;

    Test(int a) : m_a(a) {
    }

    Test() : Test(1) {
    }
};
```