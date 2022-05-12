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
draft: false
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

![执行echo $PATH的结果](../../../static/blog/posts/tech/2022-05-12_vim_install/echo_path.png)

这里/usr/local/bin优先于/usr/bin, 一般都是如此。

**/lib**是内核级的, **/usr/lib**是系统级的, **/usr/local/lib**是用户级的.

- **/lib/** — 包含许多被 /bin/ 和 /sbin/ 中的程序使用的库文件。目录 /usr/lib/ 中含有更多用于用户程序的库文件。/lib目录下放置的是/bin和/sbin目录下程序所需的库文件。/lib目录下的文件的名称遵循下面的格式：
libc.so.*
ld*
仅仅被/usr目录下的程序所使用的共享库不必放到/lib目录下。只有/bin和/sbin下的程序所需要的库有必要放到/lib目录下。实际上，libm.so.*类型的库文件如果被是/bin和/sbin所需要的，也可以放到/usr/lib下。

/bin/ — 用来贮存用户命令。目录 /usr/bin 也被用来贮存用户命令。

/sbin/ — 许多系统命令（例如 shutdown）的贮存位置。目录 /usr/sbin 中也包括了许多系统命令。

/root/ — 根用户（超级用户）的主目录。

/mnt/ — 该目录中通常包括系统引导后被挂载的文件系统的挂载点。譬如，默认的光盘挂载点是 /mnt/cdrom/.

/boot/ — 包括内核和其它系统启动期间使用的文件。

/lost+found/ — 被 fsck 用来放置零散文件（没有名称的文件）。

/lib/ — 包含许多被 /bin/ 和 /sbin/ 中的程序使用的库文件。目录 /usr/lib/ 中含有更多用于用户程序的库文件。

/dev/ — 贮存设备文件。

/etc/ — 包含许多配置文件和目录。

/var/ — 用于贮存variable（或不断改变的）文件，例如日志文件和打印机假脱机文件。

/usr/ — 包括与系统用户直接有关的文件和目录，例如应用程序及支持它们的库文件。

/proc/ — 一个虚拟的文件系统（不是实际贮存在磁盘上的），它包括被某些程序使用的系统信息。

/initrd/ — 用来在计算机启动时挂载 initrd.img 映像文件的目录以及载入所需设备模块的目录。

警告

不要删除 /initrd/ 目录。如果你删除了该目录后再重新引导 Red Hat Linux 时，你将无法引导你的计算机。

/tmp/ — 用户和程序的临时目录。 /tmp 给予所有系统用户读写权。

/home/ — 用户主目录的默认位置。

/opt/ — 可选文件和程序的贮存目录。该目录主要被第三方开发者用来简易地安装和卸装他们的软件包。

