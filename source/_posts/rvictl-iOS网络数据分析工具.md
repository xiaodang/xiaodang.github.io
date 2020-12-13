
---
title: rvictl-iOS网络数据分析工具
date: 2020-12-13 20:16:15
tags: macOS
---
之前在iPhone上抓包都是用的`Charlse`,但是只能捕获HTTP和HTTPS对流量。

今天发现了苹果的一个工具叫`rvictl`，rvi是Remote Virtual Interface的缩写，表示远程虚拟接口。把iPhone通过USB连接电脑之后，通过rvictl就可以创建一个虚拟接口。接下来就可以用tcpdump或者wireshark来监听这个网络接口上的流量。

但是，由于系统升级了Big Sur，目前这个工具在我的电脑上暂时无法使用。只能等下来的版本再测试一下。