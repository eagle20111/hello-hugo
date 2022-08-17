---
title: "2022 08 01_C++_Data_Structure"
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
## Here I document some notes about data structure types about c++

- stack
- map/multimap
- set/multiset(底层实现机制)
- unordered_map/unordered_set
- queue/priority_queue/deque
- list
- TreeNode

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) { }
}
```