---
title: "Build VIM 8.2 from source"
slug: ""
date: 2022-05-11T10:00:13+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: []
katex: false
mermaid: false
draft: true
---

# VIM 8.2 安装

## 1. 首先从github下载源码vim 8.2

- run the following command to downlaod source code of VIM from github

```shell
git clone git@github:
```

# Linux系统各系统文件夹下的区别

首先，usr 指 Unix System Resource，而不是User。
通常，
**/usr/bin**下面的都是系统预装的可执行程序，会随着系统升级而改变。

**/usr/local/bin**目录是给用户放置自己的可执行程序的地方，推荐放在这里，不会被系统升级而覆盖同名文件。

如果两个目录下有相同的可执行程序，谁优先执行受到PATH环境变量的影响，比如我的一台服务器的PATH变量为。
```shell
echo $PATH
```

![](/home/yejian/Documents/hello-hugo/static/blog/posts/tech/2022-05-12_vim_install/echo_path.png)
这里/usr/local/bin优先于/usr/bin, 一般都是如此。
