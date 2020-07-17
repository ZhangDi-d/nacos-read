---
description: For man is man and master of his fate.
---

# 深入理解 Protobuf

## 概念

{% hint style="success" %}
Protocol buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.

翻译：Protocol buffers 是一种语言无关、平台无关的用于序列化数据的可扩展机制际上
{% endhint %}

Protocol buffers，简称 Protobuf。它是一种语言、平台无关性的序列化框架，常用于通信协议、数据存储等领域。

另外 Protobuf 最重要的就是利用定义的 **.proto** 文件生成**特殊的源代码（意思是说源代码不易读懂，Google 还是很讲究的）**，利用这个源代码我们可以非常轻松的在各种数据流中写入和读取结构化的数据。

\*\*\*\*❤ **Protobuf 优点：**

* 语言、平台无关
* 对于结构化数据优势明显；相对 JSON、XML 更小、更快，压缩数据也更小
* 扩展性、兼容性好；你可以只更新定义的 .proto 文件而不影响原有已部署的程序

💔 **Protobuf 缺点：**

* 需要定义 **.proto** 文件，根据定义的 **.proto** 文件生成相应语言的代码，另一方面还需要对生成的代码（如 Java 类）进行序列化和反序列化
* **.proto** 文件和生成的代码类相对而言难以读懂

\*\*\*\*🌠 **Protobuf 支持的语言**

目前 Protobuf 可以支持 Java, Python, Objective-C, C++ 等进行特殊源代码生成，在最新的 proto3 版本中，开始支持 Dart, Go, Ruby, C\# 等语言

### Protobuf 语法

> 详细语法格式请参考文末提供的官方指南

{% tabs %}
{% tab title="定义一个消息" %}
```text
// 使用 proto3 版本，默认是使用 proto2 版本协议
syntax = "proto3";

// 生成多个 Java 文件，如果生成单个文件更加的难以阅读
option java_multiple_files = true;
// 生成的文件保存的路径
option java_package = "com.alibaba.nacos.consistency.entity";

// 可以看到，消息类型是可以定义多个的

// 定义一个 Log 消息；内部字段类型、值、唯一编号定义 name value = index;
message Log {
  string group = 1;
  string key = 2;
  bytes data = 3;
  string type = 4;
  string operation = 5;
  map<string, string> extendInfo = 6;
}

// 定义一个 GetRequest 消息
message GetRequest {
  string group = 1;
  bytes data = 2;
  map<string, string> extendInfo = 3;
}

// 定义一个 Response 消息
message Response {
  bytes data = 1;
  string errMsg = 2;
  bool success = 3;
}
```
{% endtab %}

{% tab title="Second Tab" %}
```text
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```
{% endtab %}
{% endtabs %}

{% hint style="success" %}
Protobuf 中指定的类型与对应语言生成的数据类型参照表
{% endhint %}

| .proto Type | Notes | Java Type | Python Type\[2\] | Go Type |
| :--- | :--- | :--- | :--- | :--- |
| double |  | double | float | float64 |
| float |  | float | float | float32 |
| int32 | 使用可变长度编码。负数编码效率低下–如果您的字段可能具有负值，请改用sint32 | int | int | int32 |
| int64 | 使用可变长度编码。负数编码效率低下–如果您的字段可能具有负值，请改用sint64 | long | int/long\[3\] | int64 |
| uint32 | 使用可变长度编码 | int\[1\] | int/long\[3\] | uint32 |
| uint64 | 使用可变长度编码 | long\[1\] | int/long\[3\] | uint64 |
| sint32 | 使用可变长度编码。有符号的int值。与常规int32相比，它们更有效地编码负数 | int | int | int32 |
| sint64 | 使用可变长度编码。有符号的int值。与常规int64相比，它们更有效地编码负数 | long | int/long\[3\] | int64 |
| fixed32 | 始终为四个字节。如果值通常大于228，则比uint32更有效 | int\[1\] | int/long\[3\] | uint32 |
| fixed64 | 始终为八个字节。如果值通常大于256，则比uint64更有效 | long\[1\] | int/long\[3\] | uint64 |
| sfixed32 | 始终为四个字节 | int | int | int32 |
| sfixed64 | 始终为八个字节 | long | int/long\[3\] | int64 |
| bool |  | boolean | bool | bool |
| string | 字符串必须始终包含UTF-8编码或7位ASCII文本，并且不能超过232 | String | str/unicode\[4\] | string |
| bytes | 可以包含不超过232个任意字节序列 | ByteString | str | \[\]byte |

### Protobuf 之 Java Tutorial

> Java 使用 protobuf 实际上也很容易，通过下面三个步骤即可

* Define message formats in a `.proto` file.（定义一个 .proto 消息格式化文件）
* Use the protocol buffer compiler. （使用 protobuf 编译器编译这个文件）
* Use the Java protocol buffer API to write and read messages.（使用 Java Protobuf API 轻松的实现结构化消息的读取、写入）

当然这不是在 Java 中使用 Protobuf 的全面指南。有关更多详细的参考信息，请参考 [Protocol Buffer Language Guide](https://developers.google.com/protocol-buffers/docs/proto), [Java API Reference](https://developers.google.com/protocol-buffers/docs/reference/java), [Java Generated Code Guide](https://developers.google.com/protocol-buffers/docs/reference/java-generated), 以及 [Encoding Reference](https://developers.google.com/protocol-buffers/docs/encoding)









### Reference

* [protobuf 3.8.0 Download](https://github.com/protocolbuffers/protobuf/releases/tag/v3.8.0)
* [protocol-buffers guide](https://developers.google.com/protocol-buffers/docs/overview)



