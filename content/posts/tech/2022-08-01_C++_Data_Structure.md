---
title: "C++ STL (Standard Template Library) Containers"
slug: ""
date: 2022-08-01T09:01:45+08:00
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
## C++ STL (Standard Template Library) 总结

C++ STL 容器是使用频率超高的基础设施，只有了解各个容器的底层原理，才能得心应手地用好不同的容器，做到用最合适的容器干最合适的事情。

本文旨在对 C++ 标准模板库的 _array_, _vector_, _deque_, _list_, _forward_list_, _queue_, _priority_queue_, _stack_, _map_, _multimap_, _set_, _multi_set_, _unordered_map_, _unordered_multimap_, _unordered_set_, _unordered_multiset_ 共十六类容器进行系统的对比分析，重点关注各个容器的底层原理与性能特点。本文唯一参考资料为C++官方文档，若有其它参考则会指明出处。

### 1. array

> Container properties: Sequence | Contiguous storage | Fixed-size aggregate
> 容器属性：顺序容器（支持随机访问），连续内存空间，固定大小；//连续内存
> 类模板头：template < class T, size_t N > class array;

array 即数组，其大小固定，所有的元素严格按照内存地址线性排列，array 并不维护元素之外的任何多余数据，甚至也不会维护一个size这样的变量，这保证了它在存储性能上和C++语法中的数组符号[]无异。尽管其它大部分标准容器都可以通过 std::allocator 来动态的分配和回收内存空间，但 Array 并不支持这样做。

Array 和其它标准容器一个很重要的不同是：对两个 array 执行 swap 操作意味着真的会对相应 range 内的元素一一置换，因此其时间花销正比于置换规模；但同时，对两个 array 执行 swap 操作不会改变两个容器各自的迭代器的依附属性，这是由 array 的 swap 操作不交换内存地址决定的。

Array 的另一个特性是：不同于其它容器，array 可以被当作 std::tuple 使用，因为 array 的头文件重载了get()以及tuple_size()和tuple_element()函数（注意这些函数非 array 的成员函数，而是外部函数）。

最后需要注意，虽然 array 和 C++语法中的[]符号无限接近，但两者是两个存在，array 毕竟是标准模板库的一员，是一个class，因此支持begin(), end(), front(), back(), at(), empty(), data(), fill(), swap(), ... 等等标准接口，而[]是真正的最朴素的数组。

