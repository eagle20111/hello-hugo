---
title: "Ubuntu 22.04 | Install zsh as default shell"
slug: ""
date: 2022-05-09T11:54:06+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: ["zsh"]
katex: false
mermaid: false
draft: false
---

## zsh说明

- zsh是一个Linux下强大的shell, 由于大多数Linux产品安装以及默认使用bash shell, 但是丝毫不影响极客们对zsh的热衷, 几乎每一款Linux产品都包含有zsh，通常可以用apt-get、urpmi或yum等包管理器进行安装.

- zsh是bash的增强版，其实zsh和bash是两个不同的概念，zsh更加强大。

- 通常zsh配置起来非常麻烦，且相当的复杂，所以oh-my-zsh是为了简化zsh的配置而开发的，因此oh-my-zsh算是zsh的配置.

## 准备
 - 查看当前系统用shell版本
```shell
echo $SHELL
```

- 查看系统自带哪些shell
```shell
cat /etc/shells
```

## 安装zsh
```shell
sudo apt install zsh
```

## zsh配置

将zsh设置为默认的shell
```shell
chsh -s /bin/zsh
```

然后重启电脑
```shell
reboot
```

## 安装oh-my-zsh及其个性化配置

### 安装oh-my-zsh

```shell
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
或者
```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### 主题配置

打开配置文件~/.zshrc

编辑
```txt
ZSH_THEME="xxf"
```

xxf.zsh-theme文件下载地址: [xxf.zsh-theme文件下载](https://github.com/xfanwu/oh-my-zsh-custom-xxf/blob/master/themes/xxf.zsh-theme)  

下载之后将文件拷贝到以下路径: ```/home/username/.oh-my-zsh/themes/```

### 插件

#### 安装自动补全插件incr

```shell
cd ~/.oh-my-zsh/plugins/
mkdir incr && cd incr
wget http://mimosa-pudica.net/src/incr-0.2.zsh
```
编辑~/.zshrc文件，添加以下内容:
```
source ~/.oh-my-zsh/plugins/incr/incr*.zsh
```
然后，source一下:
```shell
source ~/.zshrc
```

#### 直接使用默认插件

在~/.zshrc文件中添加插件:
 
```txt
plugins=(git extract z)
```

#### 安装autojump插件
```shell
sudo apt install autojump
```

在~/.zshrc文件中编辑:
```
. /usr/share/autojump/autojump.sh
```
然后，source一下:
```shell
source ~/.zshrc
```

#### 安装zsh-syntax-highlighting语法高亮插件

从gihub下载源码并放在~/.oh-my-zsh/plugins/文件夹下:
```shell
cd ~/.oh-my-zsh/plugins/
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

在~/.zshrc文件中编辑:
```
source ~/.oh-my-zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```
然后，source一下:
```shell
source ~/.zshrc
```

#### 安装zsh-autosuggestions语法历史记录插件

从gihub下载源码并放在~/.oh-my-zsh/plugins/文件夹下:
```shell
cd ~/.oh-my-zsh/plugins/
git clone git@github.com:zsh-users/zsh-autosuggestions.git
```

在~/.zshrc文件中编辑:
```
source ~/.oh-my-zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
```
然后，source一下:
```shell
source ~/.zshrc
```

### 其他

- 设置更新日期
    在~/.zshrc文件中编：
    ```
    exprot UPDATE_ZSH_DAYS=13
    ```
- 禁止自动更新
    ```
    DISABLE_AUTO_UPDATE="true"
    ```
- 手动更新oh-my-zsh
    ```shell
    upgrade_oh_my_zsh
    ```
- 卸载oh-my-zsh
    ```shell
    uninstall_on_my_zsh zsh
    ```
