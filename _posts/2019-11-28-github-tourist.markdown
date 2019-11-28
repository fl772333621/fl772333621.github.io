---
layout:     post
title:      "GIT常用命令"
subtitle:   "git命令"
date:       2019-11-28 15:30:00
author:     "MengWei"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - GIT
    - remote

---

# 1 GIT常用命令

## 给已有项目添加本地Git管理，并提交到远程

新建一个项目，如何push到远程的空白github上呢？

1. cd到项目根目录下
2. 在终端执行`git init`命令
3. 在终端执行`git add * `命令
4. 在终端执行`git commit -m 'init'`命令
5. 在终端执行`git remote add origin https://github.com/user/project_name.git`命令
6. 在终端执行`git push set-upstream origin master`命令