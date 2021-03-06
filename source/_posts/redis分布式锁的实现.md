---
title: redis分布式锁的实现
date: 2020-11-02 20:03:12
categories: 编程
tags: redis
toc: false
---
在并发场景下，经常需要对某些资源进行互斥的操作。在单服务器系统我们常用本地锁来避免并发带来的问题，然而，当服务采用集群方式部署时，本地锁无法在多个服务器之间生效，这时候保证数据的一致性就需要分布式锁来实现。

比如用户在网页和手机端同时下单，对余额进行减操作，如果没有合理的锁对余额进行保护，那最终用户对余额很有可能就会变得错乱。
```
用户A网页端                               用户A手机端

1 balance=db.getUserBalance()
2                                        balance=db.getUserBalance()
3 db.saveUserBalance(balance-100)
4                                        db.saveUserBalance(balance-100)

```

## redis单例模式下的正确实现
要正确的实现锁，必须保证:
* 互斥 - 同一时间只能有一个客户端持有锁
* 无死锁 - 即使锁定资源的客户端崩溃了，其他客户端也可以获得锁
* 锁的身份验证 - 锁的释放必须由锁的持有客户端释放，保证锁的安全释放

在redis中可以通过设置`SET NX PX `给资源上锁，NX表示只会在资源不存在的情况下设置成功，PX表示这个资源的过期时间(单位:毫秒),同时key的值被设置成了一个随机的值，这个值必须在所有的客户端中保证唯一。
``` redis
  SET resource_name my_random_value NX PX 30000
```
`NX`保证了资源的互斥

`PX`保证了无死锁。即便持有锁的客户端崩溃了，也会在过期时间之后，释放锁

`my_random_value`随机值保证了锁的安全释放。如果持有锁的A客户端由于某些阻塞操作导致超过了锁的过期时间，于是锁被自动释放，B客户端获取到了锁，而A又主动释放锁，此时A就会释放B所持有的锁。而随机值可以在A客户端删除锁时验证锁是否由A设置。

判断随机值，并且删除资源的操作也必须保证原子行，而`redis`没有这样的命令，所以必须通过`lua`脚本来实现:
```lua
  if redis.call("get",KEYS[1]) == ARGV[1] then
      return redis.call("del",KEYS[1])
  else
      return 0
  end
```




