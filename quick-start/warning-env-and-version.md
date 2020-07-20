---
description: 'Be just to all, but trust not all.'
---

# 环境与版本

新手入坑 `Nacos`，一定要注意环境要求以及知道如何选择合适的 `Nacos` 版本，防止踩坑！（尤其是在项目集成 `Nacos` 时不清楚各个版本与 `SpringBoot`、`SpringCloud` 的关系则很容易踩坑，遇到一些奇奇怪怪的问题）

## **环境要求**

> Nacos 基本环境要求

* 64 bit OS
* 64 bit JDK 1.8+
* Maven 3.2.x+

## **版本选择**

> 官方提供的版本关系图：[版本说明](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)

在了解 `Nacos` 的环境要求之前，我们需要知道如何正确的选择 `Nacos` 的版本，因为很多初入坑 `Nacos` 的人会因为不清楚版本的关系导致白白浪费了时间，因为最开始文档还不完善的时候就只有自己去踩坑了！

{% hint style="danger" %}
**额外说明**：关于 `Spring Cloud Alibaba` 仓库迁移
{% endhint %}

因为 `Spring Cloud` 官方修改了各个第三方项目的发布策略，因此才有了：[通知: Spring Cloud Alibaba 仓库迁移](https://blog.csdn.net/xxscj/article/details/96310527)，这也是为什么我们去搜索 Nacos 的仓库时会看到两个版本：

![](https://github.com/rexlin600/nacos-read/tree/18c5a5d64f2b54fd456cb7ccfcd66fd5eb30aae7/summary/images/screenshot_1594484383951.png)

也正是基于需要前面参考链接中所描述的原因，后续我们使用 `Nacos` 请使用 `Alibaba` 提供的依赖即可&gt; Nacos 与 SpringBoot 版本如何选择？

在你使用 `SpringBoot` 集成 `Nacos` 时可能会引入如下 `starter`，如下：

```markup
<!-- https://mvnrepository.com/artifact/com.alibaba.boot/nacos-config-spring-boot-starter -->
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>nacos-config-spring-boot-starter</artifactId>
    <version>${lastest.version}</version>
</dependency>
```

需要注意的是，其中你选择 `${lastest.version}` 如果是 `0.2.x.RELEASE` 则对应的是 `SpringBoot 2.x` 版本；如果是 `0.1.x.RELEASE` 则对应的是 `SpringBoot 1.x` 版本&gt; Nacos 与 SpringCloud 版本如何选择?

同上，在我们引入相应依赖时需要知道这个原则：

```markup
<!-- 服务发现与注册 -->
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
     <version>${lastest.version}</version>
</dependency>

<!-- 配置中心 -->
<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-config -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
     <version>${lastest.version}</version>
</dependency>
```

* 版本 `2.1.x.RELEASE` 对应的是 `Spring Boot 2.1.x` 版本
* 版本 `2.0.x.RELEASE]` 对应的是 `Spring Boot 2.0.x` 版本
* 版本 `1.5.x.RELEASE` 对应的是 `Spring Boot 1.5.x` 版本

