---
title: cpp学习
author: ygg
date: 2023-10-26 14:10:00 +0800
categories: [Blogging, Tutorial]
tags: [writing]
math: true
---

## lambda表达式

```cpp
void test_lambda() {
    int a[]() {};
}
```

## 手动管理内存

在函数体中定义的临时变量将在函数声明周期结束时被析构，这些变量存储在寄存器或者栈内存之中，清除它们比较容易。动态申请的内存需要使用delete关键字手动地析构，否则就会发生内存泄漏等问题。

```cpp
class densy {
public:
    densy(){std::cout << "Ctor Called!" <<std::endl;}
    ~densy(){std::cout << "Dtor Called!" << std::endl;}
};

int main(int argc, char* args[]) {
    densy* ptr = new densy();
    densy* ptr_ = new densy[2];
    delete ptr;
    delete []ptr_; // delete for array type
}
```

如果没有手动析构动态申请的内存，就会产生内存泄漏（memory leak）。

悬挂指针（dangling pointer）是指一个指针指向已经释放或无效的内存地址。当一个指针被用来引用已经释放的内存区域或者超出了其作用域的变量时，就会产生悬挂指针。

两次释放（double free）指的是对同一个对象作了两次及以上析构，这可能是由于不同调用层级的函数都对使用的对象作了析构操作。

## 智能指针类型

在memory头文件中定义了三种智能指针，包括`shared_ptr`和`unique_ptr`和`weak_ptr`这三种。