# **代码目录层次**

> 再来看下 `naming` 模块的项目代码组织结构

```
➜  naming git:(feature-1.3.1) tree -I "test|*.iml" -d
└── src
    └── main
        ├── java
        │   └── com
        │       └── alibaba
        │           └── nacos
        │               └── naming        // naming 模块下的所有包，如下：
        │                   ├── cluster    // 集群服务状态管理
        │                   │   └── transport    // 自定义 jackson 序列化
        │                   ├── consistency    // 一致性，nacos 实现 CAP 中的 AP、CP
        │                   │   ├── ephemeral    // AP
        │                   │   │   └── distro
        │                   │   └── persistent    // CP
        │                   │       └── raft
        │                   ├── controllers    // naming 模块提供的接口
        │                   ├── core             // naming 模块核心实现（集群、实例、服务、订阅 etc）
        │                   ├── exception    // 全局异常拦截器
        │                   ├── healthcheck // 健康检查
        │                   │   ├── events
        │                   │   └── extend
        │                   ├── misc            // 杂项，工具等
        │                   ├── monitor        // 监控
        │                   ├── pojo             // 实体对象
        │                   ├── push            // Application Event 事件推送
        │                   ├── selector       // naming 定义的 Select 接口，可用于自定义实现服务发现的负载均衡策略
        │                   └── web             // Filter、Config 等
        └── resources        // 资源目录
            └── META-INF
                └── logback

29 directories
```

</br>
