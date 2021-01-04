---
title: monkeyDev开发
date: 2020-12-03 21:53:26
categories: 编程
tags:
---
今天进行iOS逆向开发，肯定会遇到很多坑，之前的坑都踩过，但是没有记录，这次记录一下。
### 坑1 签名失败
安装完后mokeyDev之后，在XCode创建一个新项目，但是对于一直报错无法签名，最后查资料可以在target的Build Settings里面增加一个自定义配置，`CODE_SIGNING_ALLOWED=NO`，最后变异成功。
### 坑2 无法找到libstdc++类库
这个比较常见，是因为xcode移除了libstdcc，如果类库依赖的话，只需要找到放在对应目录下就可以。


### 如何查看ipa是否加密
otool -l Target | grep -A 4 LC_ENCRYPTION_INFO

### cycrpt 打印当前界面的UI结构
```
cy# UIApp.keyWindow.recursiveDescription().toString()
c# [[UIApp keyWindow] _autolayoutTrace].toString()
```
### find controller
var a = UIApp.windows[0].rootViewController.childViewControllers
### lldb debug by address
debugserver localhost:9999 -x backboard -a 7844
breakpoint set -a 0x00000012
po [myObject valueForKey:@"aProperty"]
查找内存地址 正则表达式 image lookup -rn
breakpoint set -n "-[UIViewController viewDidLoad]" -C "po $arg1" -G1 // 打印所有符合条件的实例对象

### frida
frida --codeshare mrmacete/objc-method-observer -U  -p 6740
observeClass()
observeSomething("-[NSURLRequest *setValue:forHTTPHeaderField*]")




