# **代码目录层次**

> consistency 模块代码层次结构

```text
E:.
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─alibaba
│  │  │          └─nacos
│  │  │              └─consistency // consistency 包
│  │  │                  │  CommandOperations.java // 操作维护命令界面接口
│  │  │                  │  Config.java // 配置
│  │  │                  │  ConsistencyProtocol.java // 一致性协议
│  │  │                  │  IdGenerator.java // ID生成器
│  │  │                  │  LogProcessor.java // 日志函数是处理器
│  │  │                  │  ProtocolMetaData.java // 协议元数据
│  │  │                  │  SerializeFactory.java // 序列化工厂类
│  │  │                  │  Serializer.java // 序列化接口
│  │  │                  ├─ap //  AP 协议
│  │  │                  ├─cp // CP 协议
│  │  │                  ├─entity // 实体类
│  │  │                  ├─exception // 异常
│  │  │                  ├─serialize // 序列化
│  │  │                  └─snapshot // 快照
│  │  ├─proto // protobuf协议
│  │  └─resources // 资源目录
│  └─test // 测试目录
```

## **模块依赖**

在阅读 `consistency` 模块之前，有必要了解一下其引入的依赖包从而通过引入的依赖包去分析模块可能具备的能力：

```xml
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

- 某个包的功能是什么？
- 引入的包在哪儿被使用？

关于上面的问题将在模块的文章内陆续被讲到，请同学们耐住性子继续往后看。

</br></br>
