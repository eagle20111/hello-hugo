---
title: "Git command cheat sheet"
slug: ""
date: 2022-05-11T09:59:56+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: ["git"]
katex: false
mermaid: false
draft: true
---

1. **git rebase**
   ref: https://git-scm.com/docs/git-rebase
   - **用法一:** 将topic分支的base由E改为master
        ```shell
            A---B---C topic
            /
        D---E---F---G master
        ```
        运行:
        ```shell
        git rebase master
        git rebase master topic
        ```
        结果:
        ```shell
                    A'--B'--C' topic
                    /
        D---E---F---G master
        ```
    - **用法二:**
2. **cherrypick**
   
3. **git submodule**
   - 将一个repo添加为submodule
        ```shell
        ```