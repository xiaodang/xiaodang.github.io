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
该参数为设置了4秒。但是有些请求竟然1分15秒才返回。

curl对代理进行测试结果如下，由此可见timeout参数并没有生效。
```
time curl  -vvv -x http://106.5.193.161:4245 baidu.com
*   Trying 106.5.193.161...
* TCP_NODELAY set
* Connection failed
* connect to 106.5.193.161 port 4245 failed: Operation timed out
* Failed to connect to 106.5.193.161 port 4245: Operation timed out
* Closing connection 0
curl: (7) Failed to connect to 106.5.193.161 port 4245: Operation timed out
curl -vvv -x http://106.5.193.161:4245 baidu.com  0.00s user 0.01s system 0% cpu 1:15.76 total
```
于是猜测应该是tcp重试机制导致的，于是打开`Wireshark`观察网络请求，发现系统尝试发送了10次SYN数据包。
![](https://cdn.jsdelivr.net/gh/xiaodang/blog-image/img/20210203092249.png)

定位到原因之后，有两个解决办法，第一个是在应用层强制超时，第一个是修改系统tcp的重试参数。

为了尽快解决问题，选择第二种方案，由于线上是CentOS，只需要执行即可临时修改syn数据包的重试次数为1。修改后再次通过curl测试，发现可以很快返回超时。
```
sysctl -w net.ipv4.tcp_syn_retries=1
```
