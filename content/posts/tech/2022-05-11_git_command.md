---
title: "Git Command Notes"
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
draft: false
---
## git command record as my cheatsheet
1. **<font color=red>git rebase</font>**
   ref: https://git-scm.com/docs/git-rebase
   - **用法一:```git rebase <branch-name>```** 将topic分支的base由E改为master
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
        > if upstream branch already has a change like below:
        ```shell
              A---B---C topic
             /
        D---E---A'---F master
        ```
        then run the command ```git rebase master```, you will get following result:
        ```shell
                       B'---C' topic
                      /
        D---E---A'---F master
        ``` 
    - **用法二:```git rebase --onto```**
        assume **topic** is based on **next**, and **next** is based on master
        ```shell
            o---o---o---o---o  master
                \
                o---o---o---o---o  next
                                \
                                o---o---o  topic
        ```
        run the command below:
        ```shell
        git rebase --onto master next topic
        ```
        then we get the result below:
        ```shell
        o---o---o---o---o  master
            |            \
            |             o'--o'--o'  topic
             \
              o---o---o---o---o  next
        ```
        Another example:
        A range of commits could also be removed with rebase. If we have the following situation:
        ```shel l
        E---F---G---H---I---J  topicA
        ```
        then the command
        ```shell
        git rebase --onto topicA~5 topicA~3 topicA
        ```
        would result in the removal of commits F and G:
        ```shell
        E---H'---I'---J'  topicA
        ```
    - **用法三:```git rebase -i <commit_id> <commit_id>```** $\mathbb{\rightarrow}$ 将多个commit合并为一个。
        ```shell
        # 执行git log，得到以下commit_ids
        >>>21fd585 
        >>>45j3483
        >>>9i8975d
        >>>73c20ec
        ```
        目标: 将**21fd585**、**45j3483**、**9i8975d** rebase 到 **73c20ec**
        ```shell
        git rebase -i 73c20ec  21fd585 
        ```
        得到:
        ```shell
        pick
        pick
        pick
        pick
        ```
        改为
        ```shell
        pick 
        squash
        squash
        squash
        ```
        最后，编辑commit内容，
        得到
        ```shell
        >>>b8bec33 # 此处为新的commit
        >>>73c20ec
        ```
        推送到remote:
        ```shell
        git push -f origin master
        ```
        ref: 
        1. https://www.bilibili.com/video/BV15h411f74h/
        2. https://blog.csdn.net/weixin_45953517/article/details/114362752
        3. https://blog.csdn.net/weixin_44691608/article/details/118740059#t7
        > 遇到detached HEAD的解决办法

        ```shell
        git branch b1
        git checkout master
        git merge b1
        git push origin master
        git branch -d b1
        ```    

2. **<font color=red>git cherrypick</font>**
   - 将指定的提交用于其他分支
        例如:
        ```shell
        a - b - c - d   Master
             \
              e - f - g Feature
        ```
        run the command below and apply commit(f) to master
        ```shell
        git checkout master
        git cherry-pick f
        ```
        get the result
        ```shell
        a - b - c - d - f   Master
             \
              e - f - g Feature
        ```
    - 转移多个提交
        ```shell
        # 将 A 和 B 两个提交应用到当前分支
        git cherry-pick <HashA> <HashB>
        ```
        或者
        ```shell
        # 该命令可以转移从 A 到 B 的所有提交,它们必须按照正确的顺序放置：提交 A 必须早于提交 B，否则命令将失败，但不会报错。
        git cherry-pick A..B 
        ```
        ```shell
        # 使用上面的命令，提交 A 将不会包含在 Cherry pick 中， 如果要包含提交 A，可以使用下面的语法。
        git cherry-pick A^..B
        ```
        ref:https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html
        
3. **<font color=red>git submodule</font>**
   - 将一个repo添加为submodule
        ```shell
        git submodule add https://github.com/chaconinc/DbConnector
        ```
   - 克隆含有子模块的项目
        ```shell
        git clone https://github.com/chaconinc/MainProject #此时包含子模块目录，但是其中没有任何文件
        cd MainProject
        cd DbConnector/
        # 此时有DbConnector目录，但是文件夹是空的
        git submodule init # 用来初始化本地配置文件
        git submodule update # 从该项目中抓取并检出父项目中列出的合适的提交
        ```
        或者
        ```shell
        git clone --recurse-submodules https://github.com/chaconinc/MainProject
        ```
        或者已经克隆了项目，但是忘记```--recurse-submodule```, 则使用
        ```shell
        git submodule update --init --recursive
        ```

4. **<font color=red>拉取远程分支到本地</font>**
    ```shell
    git fetch origin remote-branch-name
    git checkout -b local-branch-name origin/remote-branch-name
    ```