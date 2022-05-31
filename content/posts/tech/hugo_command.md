---
title: "Hugo_command"
slug: ""
date: 2022-05-02T13:57:29+08:00
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

# Commands for creating sites backend with hugo

1. create a new markdown file
    ```shell
    hugo new posts/tech/name-of-file.md  
    hugo new content/posts/tech/name-of-file.md
    ```

2. create new site
    ```shell
    hugo new site name
    ```

3. Install hugo 

- Linux
    ```shell
    wget https://github.com/gohugoio/hugo/releases/download/v0.83.1/hugo_0.83.1_Linux-64bit.tar.gz
    tar -xf hugo_0.83.1_Linux-64bit.tar.gz
    sudo mv hugo /usr/local/bin/
    hugo version
    ```