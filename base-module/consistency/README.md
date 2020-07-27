---
description: Big mouthfuls ofter choke.
---

# Consistency 模块

## 模块结构

> consistency 模块代码目录层次结构

```text
➜  main git:(feature-1.3.1) tree -d
.
├── java
│   └── com
│       └── alibaba
│           └── nacos
│               └── consistency
│                   ├── ap
│                   ├── cp
│                   ├── entity
│                   ├── exception
│                   ├── serialize
│                   └── snapshot
└── proto

12 directories

```

## **模块依赖**

在阅读 `consistency` 模块之前，有必要了解一下其引入的依赖包从而通过引入的依赖包去分析模块可能具备的能力：

```markup
<dependencies>
    <!-- common包 -->
    <dependency>
        <groupId>${project.groupId}</groupId>
        <artifactId>nacos-common</artifactId>
    </dependency>
    <!-- lib：Java元组数据包 -->
    <dependency>
        <groupId>org.javatuples</groupId>
        <artifactId>javatuples</artifactId>
    </dependency>
    <!--lib：hessian协议包 -->
    <dependency>
        <groupId>com.caucho</groupId>
        <artifactId>hessian</artifactId>
    </dependency>
    <!-- lib：protobuf协议包 -->
    <dependency>
        <groupId>com.google.protobuf</groupId>
        <artifactId>protobuf-java</artifactId>
    </dependency>
</dependencies>
```

到这里实际上我们就可以给自己提至少两个问题（后续不再这样描述，这里只是给大家提供一种思考、剖析源码的方法），例如：

* 某个包的功能是什么？
* 引入的包在哪儿被使用？

关于上面的问题将在模块的文章内陆续被讲到，请同学们耐住性子继续往后看。

## 问题列表

* [x] 聊聊元组 Tuple（扩展知识）
* [x] 详解 nacos 中的 IdGeneratorManager
* [x] SPI 与 JDBC 驱动自动加载原理、 ServiceLoad 解析（扩展知识）
* [x] 理解 Protobuf（扩展知识）
* [x] 理解 Hessian（扩展知识）
* [x] Base理论与 CAP 原则（扩展知识）
* [x] 理解 Raft 算法（扩展知识）
* [x] 蚂蚁金服开源 SOFAJRaft
* [ ] 事件发布与订阅、观察者模式
* [x] Nacos 中的一致性协议层



