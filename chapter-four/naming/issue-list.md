---
description: Business is business.
---

# 问题列表

> nacos-naming 模块的 issues

* `naming`模块是什么、提供什么能力？
* `NamingService`是什么、提供了哪些方法？
* 创建`NamingService`需要传入哪些参数？
* 为什么不能直接从源码启动`naming`模块，会提示  

  `No qualifying bean of type 'com.alibaba.nacos.core.auth.AuthManager ...`

* `NamingFactory`是如何创建`NamingService`的？
* `NamingService`在`nacos-all`的哪个模块下，结构是怎样的？
* `NamingService`的创建过程是怎样的？其中运用了哪些技术、知识？
* 实例`instance`是如何注册到集群上的？
* 实例`instance`是如何从集群注销的、如何自动注销实例的？
* `nacos`是怎样获取所有健康/不健康的`Instance`的？
* `nacos`是如何按照权重获取`instance`的？
* `Nacos`中的`CAP`，默认支持的是哪种？
* `naming`实例健康、心跳机制是如何实现的？
* `naming`中`raft`协议是如何落地的？  

## Reference

* [如何单独启动 naming 模块](https://github.com/alibaba/nacos/issues/3042)
* [Nacos CP/AP模式切换及微服务临时/永久实例配置](https://blog.csdn.net/weixin_43791937/article/details/106496167?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-1)

