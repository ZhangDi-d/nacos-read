---
description: Believe not all that you see nor half what you hear.
---

# 构建及部署

> 注意：这里讲构建与启动是为了照顾先要体验的同学，后面会将如何通过 IDEA 去进行调试！

一般来说，我们有两种方式来获取 `Nacos`，分别是：

* `方法一`：通过源码自行构建
* `方法二`：直接下载发行包

当然，我们还可以通过使用社区提供的诸如 `Docker` 容器版本的 `Nacos` 等方式来获取 `Nacos`；直接下载发行包就不多作说明了，这里介绍下通过源码构建的方式来获取 `Nacos`

## **通过源码构建**

> 这里推荐使用最新的稳定版，可以参考 [Nacos功能和需求列表](https://nacos.io/zh-cn/docs/feature-list.html) 中推荐大家使用的版本！

```text
# clone nacos
# 小技巧，可以将 github.com 替换为 github.com.cnpmjs.org 从而提高 clone 速度（效果显著）
git clone https://github.com/alibaba/nacos.git

# 进入目录
cd nacos/

# 构建
mvn -Prelease-nacos -Dmaven.test.skip=true clean install -U

# 查看构建后的文件
ls -al distribution/target/

# 注意变更 $version 为实际的版本目录
cd distribution/target/nacos-server-$version/nacos/bin
```

如果你不想通过源码构建的方式，可以去 [最新稳定版 Nacos](https://github.com/alibaba/nacos/releases) 下载 `nacos-server-$version.zip` 包，然后按下面方式解压即可

```text
# unzip or tar -xvf
unzip nacos-server-$version.zip

# 或者 tar -xvf nacos-server-$version.tar.gz 

# 最后进入 bin 目录即可
cd nacos/bin
```

## **启动Nacos**

在前面我们已经构建好并进入 `bin` 目录后，就可以启动 `Nacos`了，如果是本地测试的话可以直接单机模式运行即可，如下：

```text
# Linux、Unix、Mac 启动命令
sh startup.sh -m standalone

# Windows 启动命令（或者双击 startuo.cmd 文件）
bash startup.sh -m standalone
```

## **关闭Nacos**

有头有尾，这里再介绍下如何关闭 `Nacos`，也非常简单，如下：

```text
# Linux/Unix/Mac
sh shutdown.sh

# Windows（或者双击shutdown.cmd运行文件）
cmd shutdown.cmd
```

## **部署方式**

`Nacos` 支持三种部署方式：

* 单机模式 - 用于测试和单机试用
* 集群模式 - 用于生产环境，确保高可用
* 多集群模式 - 用于多数据中心场景

参考链接：[Nacos 部署参考](https://nacos.io/zh-cn/docs/deployment.html)

## **如何与 Nacos 集成**

由于 Nacos 可以将配置管理、服务注册员发现都单独部署，因此我们提供官方的集成的示例地址：[nacos-spring-cloud-example](https://github.com/nacos-group/nacos-examples/tree/master/nacos-spring-cloud-example)

