---
title: TCP超时问题调试
date: 2021-02-03 09:13:36
categories: 编程
tags:
---
最近有一个项目，使用的Node的Request类库来进行HTTP请求获取数据，每个HTTP请求都要用到短效代理。为了提高可用性，加入了重试机制，如果代理失效，则更换代理重试。为了防止代理速度过慢，设置了timeout参数，该参数在文档中的说明是：
```
timeout - integer containing number of milliseconds, controls two timeouts.
Read timeout: Time to wait for a server to send response headers (and start the response body) before aborting the request.
Connection timeout: Sets the socket to timeout after timeout milliseconds of inactivity. Note that increasing the timeout beyond the OS-wide TCP connection timeout will not have any effect (the default in Linux can be anywhere from 20-120 seconds)

```
