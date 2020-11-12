---
title: libuv初探
date: 2020-11-12 09:07:15
categories: 编程
tags: redis
toc: true
---
今天研究了一下Node使用的异步IO框架libuv,下面一张图显示了livuv在Node架构中的位置。

![](https://cdn.jsdelivr.net/gh/xiaodang/blog-image/img/20201112091257.png)

## libuv是什么
libuv是一个高性能的，事件驱动的I/O库，并且提供了跨平台（如windows, linux）的API。

## libuv架构

![](https://cdn.jsdelivr.net/gh/xiaodang/blog-image/img/20201112091634.png)

左侧部分是网络I/O相关的请求，右侧部分是由文件I/O, DNS Ops以及User code组成的请求。对于Network I/O和以File I/O为代表的另一类请求，异步处理的机制是不一样的。

对于Network I/O相关的请求， 根据OS平台不同，分别使用Linux上的epoll，OSX和BSD类OS上的kqueue，SunOS上的event ports以及Windows上的IOCP机制。

而对于File I/O为代表的请求，则使用thread pool。利用thread pool的方式实现异步请求处理，在各类OS上都能获得很好的支持。

## I/O loop

libuv强制使用异步的，事件驱动的编程风格。它的核心工作是提供一个event-loop，还有基于I/O和其它事件通知的回调函数。libuv还提供了一些核心工具，例如定时器，非阻塞的网络支持，异步文件系统访问，子进程等。

在事件驱动编程中，程序会关注每一个事件，并且对每一个事件的发生做出反应。libuv会负责将来自操作系统的事件收集起来，或者监视其他来源的事件。这样，用户就可以注册回调函数，回调函数会在事件发生的时候被调用。


![](https://cdn.jsdelivr.net/gh/xiaodang/blog-image/img/20201112092121.png)

## HELLOWORLD

了解完之后，必须要下载类库然后了自己使用一些才能更深入的了解工作机制。由于对于C的了解并不是太多，在开发环境配置、编译方面遇到了不少问题。

* 如何安装类库?
* 如何include .h文件（c的头文件）
* 如何链接编译

由于我的是macOS环境和vscode IDE，所以使用vcpkg管理工具来安装libuv的包文件。

``` shell
vcpkg install libuv
```
创建helloworld.c文件，在头部引入`#include <uv.h>`,但是提示无法找到`uv.h`,需要在`.vscode/c_cpp_properties.json`配置文件的属性includePath中加入`${vcpkgRoot}/x64-osx/include`,其实也就是体检一个path，让编译器去这里寻找`.h`文件，最后还是无法build，提示`ld: library not found for -llibuv`,google了半太难都无法找到方案，其实很简单，就是链接阶段出错了，无法找libuv，最后直接`man ld` 查看ld的参数说明，然后在`.vscode/tasks.json`的args中加入`"-I/usr/local/var/vcpkg/installed/x64-osx/include","-L/usr/local/var/vcpkg/installed/x64-osx/lib"`即可。下面是HELLOWORLD的完整代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <uv.h>
#include <uv/version.h>
#include <time.h>

int main(int argc, char const *argv[])
{
  /* code */
  printf("version:%d\n", UV_VERSION_MAJOR);
  uv_loop_t *loop = malloc(sizeof(uv_loop_t));
  uv_loop_init(loop);

  printf("Now quitting.\n");
  uv_run(loop, UV_RUN_DEFAULT);
  uv_loop_close(loop);
  free(loop);
  return 0;
}

```