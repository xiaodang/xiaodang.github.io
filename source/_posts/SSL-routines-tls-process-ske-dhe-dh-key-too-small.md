---
title: 'SSL routines:tls_process_ske_dhe:dh key too small'
date: 2021-02-28 10:56:34
categories: 编程
tags:
---
今天遇到一个问题,有一个第三方HTTP接口，在本地通过Node.JS可以正常访问，但是CentOS服务器无法访问，错误日志提示:`Error: socket hang up`

这个错误很明显是socket网络层的错误，还没有到第三方到应用层。为了严重猜想，随便换一个HTTP接口，然后用curl测试，curl返回：`curl: (35) error:141A318A:SSL routines:tls_process_ske_dhe:dh key too small`。


通过错误可以看出来是HTTPS中的SSL/TLS层的问题，通过GOOGLE，发现是目标服务器在握手中返回的加密算法的key长度不够。

通过以下命令在我的机器上执行，可以查看目标服务器返回的key长度
```bash
openssl s_client -connect xxx:443 -cipher "EDH" | grep "Server Temp Key"
openssl s_client -connect www.example.com:443 -cipher "DHE" | grep "Server Temp Key"
```
以上命令返回中包含`Server Temp Key: DH, 1024 bits`，key的长度是1024。

接着查看系统的加密策略，执行以下命令返回`DEFAULT`，继续搜索文档`The default system-wide cryptographic policy level offers secure settings for current threat models. It allows the TLS 1.2 and 1.3 protocols, as well as the IKEv2 and SSH2 protocols. The RSA keys and Diffie-Hellman parameters are accepted if they are at least 2048 bits long.`发现`DEFAULT`策略要求key的长度最少为2048

```
update-crypto-policies --show
>DEFAULT
```

修改系统的加密策略:
```
update-crypto-policies --set LEGACY
```

参考:

[USING SYSTEM-WIDE CRYPTOGRAPHIC POLICIES](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/security_hardening/using-the-system-wide-cryptographic-policies_security-hardening)
[HTTP Server Test Error "dh Key Too Small"](https://docs.thousandeyes.com/product-documentation/tests/http-server-test-error-dh-key-too-small)

