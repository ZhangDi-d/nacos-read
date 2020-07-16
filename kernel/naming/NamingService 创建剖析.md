# **简介**

> NamingService 是完成 naming 模块实现动态服务注册、发现的基石；此外它还有个兄弟类 NacosNamingMaintainService，下一篇再介绍它的兄弟类

`NamingService` 是一个接口（`interface`），在 `com.alibaba.nacos.api.naming` 包下，官方对其定义就是很简单的一句话：`Naming Service`，我们可以从 [Nacos 架构](https://nacos.io/zh-cn/docs/architecture.html) 一文以及源码中 `NamingService` 中定义的方法可以分析出 `NamingService` 提供的功能如下：

* 服务管理（实力注册、注销、查询所有实例、查询单个实例）
* 健康检查（查看实例健康状态）
* 服务订阅、取消订阅、查询订阅服务的列表
* 查询服务、查询服务状态
* 关闭服务资源

点击链接查看 [NamingService](https://github.com/rexlin600/nacos/blob/feature-1.3.1/api/src/main/java/com/alibaba/nacos/api/naming/NamingService.java)  提供的接口以了解更多详细信息。

</br>

## **NamingService 创建浅析**

源码中 `NamingService` 是通过 [api 模块](https://github.com/rexlin600/nacos/blob/feature-1.3.1/api) 中的 [NamingFactory](https://github.com/rexlin600/nacos/blob/feature-1.3.1/api/src/main/java/com/alibaba/nacos/api/naming/NamingFactory.java) 创建的，大致流程如下：

1. 构建 `Properties`
2. 调用 `NamingFactory` 的 `#createNamingService` 方法获取到 `NamingService` 实例（重点）
3. 然后通过 `NamingService` 实例调用其提供的方法

本文后续会重点介绍第 `2` 步，剖析 `NamingService` 的创建过程；创建 `NamingService` 的示例程序可以参考 [NamingExample](https://github.com/rexlin600/nacos/blob/feature-1.3.1/example/src/main/java/com/alibaba/nacos/example/NamingExample.java) 的代码（运行 `NamingExample` 需要启动 `Nacos`，可参考上一章 `IDEA 调试` 去启动 `Nacos`）。

</br>

## **源码剖析**






