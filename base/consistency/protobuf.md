---
description: For man is man and master of his fate.
---

# 深入理解 Protobuf

## 概念

{% hint style="success" %}
Protocol buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.

翻译：Protocol buffers 是一种语言无关、平台无关的用于序列化数据的可扩展机制际上
{% endhint %}

Protocol buffers，简称 Protobuf。它是一种语言、平台无关性的序列化框架，常用语通信协议、数据存储等领域。

另外 Protobuf 最重要的就是利用定义的 **.proto** 文件生成**特殊的源代码（意思是说源代码不易读懂，Google 还是很讲究的）**，利用这个源代码我们可以非常轻松的在各种数据流中写入和读取结构化的数据。

\*\*\*\*❤ **Protobuf 优点：**

* 语言、平台无关
* 相对 Json、XML 更小、更快，压缩数据也更小
* 对于结构化数据优势明显

💔 **Protobuf 缺点：**

* 需要定义 **.proto** 文件，然后根据文件生成相应语言的代码，然后还需要对这个代码（如 Java 类）进行序列化和反序列化
* **.proto** 文件和生成的代码类相对而言难以读懂

\*\*\*\*🌠 **Protobuf 支持的语言**

目前 Protobuf 可以支持 Java, Python, Objective-C, C++ 等进行特殊源代码生成，在最新的 proto3 版本中，开始支持 Dart, Go, Ruby, C\# 等语言。







### Reference

* [protocol-buffers guide](https://developers.google.com/protocol-buffers/docs/overview)



