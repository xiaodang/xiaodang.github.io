---
title: macOS如何打开新窗口执行脚本
date: 2020-11-16 21:43:47
categories: 编程
tags: macOS
toc: false
---
今天需要写一个脚本同时ssh连接到不同到服务器执行任务，并实时观察log，所以需要写一个脚本打开新的terminal，查找后发现可以用macOS的osascript命令，记录一下：

``` bash
osascript -e 'tell application "Terminal" to do script "ls"'
```

由于连接每次连接ssh都需要输入yes来信任host，可以在`～/.ssh/config`文件中添加`StrictHostKeyChecking no`来取消检查，这也可以方便脚本的执行，但是用完记得取消，否则这样做是不安全的。
