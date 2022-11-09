---
title: "面试经验Q&A"
slug: ""
date: 2022-10-09T10:06:29+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: []
katex: true
mermaid: false
draft: true
---

# C++

## C++ 基础概念
ref : https://blog.csdn.net/CSSDCC/article/details/122049204
ref : https://zhuanlan.zhihu.com/p/513450251
ref : https://blog.csdn.net/qq_52621551/article/details/122960158?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166321173916782395327136%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166321173916782395327136&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-122960158-null-null.142^v47^body_digest,201^v3^control_2&utm_term=c%E5%92%8Cc%2B%2B%E5%8C%BA%E5%88%AB&spm=1018.2226.3001.4187

一.常考C++基础概念

1.C++三大特性（封装、继承、多态）

**封装：**

>隐藏类的属性和实现细节，仅仅对外提供接口，
封装性实际上是由编译器去识别关键字public、private和protected来实现的，
体现在类的成员可以有公有成员(public)，私有成员(private)，保护成员(protected)。
私有成员是在封装体内被隐藏的部分，只有类体内说明的函数(类的成员函数)才可以访问私有成员，
而在类体外的函数是不能访问的，公有成员是封装体与外界的一个接口，
类体外的函数可以访问公有成员，保护成员是只有该类的成员函数和该类的派生类才可以访问的。
 
> 优点：隔离变化；便于使用；提高重用性；提高安全性
> 缺点：如果封装太多，影响效率；使用者不能知道代码具体实现。

**继承：**
> 被继承的是父类（基类），继承出来的类是子类（派生类），子类拥有父类的所有的特性。
 继承方式有公有继承、私有继承，保护继承。默认是**私有继承**
 
    *公有继承中父类的公有和保护成员在子类中不变，私有的在子类中不可访问。
    *私有继承中父类的公有和保护成员在子类中变为私有，但私有的在子类中不可访问。
    *保护继承中父类的公有和保护成员在子类中变为保护，但私有的在子类中不可访问。
     c++语言允许单继承和多继承，
     
    优点：继承减少了重复的代码、继承是多态的前提、继承增加了类的耦合性；
    缺点：继承在编译时刻就定义了，无法在运行时刻改变父类继承的实现；
> 父类通常至少定义了子类的部分行为，父类的改变都可能影响子类的行为；
> 如果继承下来的子类不适合解决新问题，父类必须重写或替换，那么这种依赖关系就限制了灵活性，
最终限制了复用性。
 
> 虚继承：为了解决多重继承中的二义性问题，它维护了一张虚基类表。

**<font color=red>多态的四种表现形式</font>**
ref: [多态的四种表现形式](https://blog.csdn.net/weixin_42678507/article/details/97111466?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164515036016780269828775%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164515036016780269828775&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-97111466.pc_search_result_cache&utm_term=%E5%A4%9A%E6%80%81%E7%9A%84%E8%A1%A8%E7%8E%B0%E5%BD%A2%E5%BC%8F&spm=1018.2226.3001.4187)


多态有四种表现形式:
- 运行时多态(虚函数)
- 编译时多态(模板)
- 重载
- 类型转换

运行时多态(Subtype Polymorphism/Runtime Polymorphism)

**运行时多态**就是派生类重写基类的虚函数，在调用函数里，参数为基类的指针或引用，会构成多态。我之前写过一篇多态的原理，就是在讲多态(运行时多态)在底层是怎么实现的
(ref: [C++ 多态的实现原理](https://blog.csdn.net/weixin_42678507/article/details/89467387))





































































































































# 深度学习 && 机器学习

# 强化学习

# 模型部署

# 计算机基础

