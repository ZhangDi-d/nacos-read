---
description: 'Be swift to hear, slow to speak.'
---

# 第二章 入门

在阅读源码之前建议 `fork` `Nacos` 项目并选择好你要阅读的 `Nacos`版本，然后重新拉取分支。

笔者的源码阅读仓库在：[https://github.com/rexlin600/nacos](https://github.com/rexlin600/nacos)

阅读源码一般不太可能通读，都是挑一些重点功能、实现区阅读，建议可以按照如下方式去阅读：

* 了解项目的核心功能、亮点实现
* 阅读项目提供的 example、test 等模块，看是否包含核心功能、亮点实现的简单示例，如果有自己可以尝试跑一下
* 重点攻克相应模块的核心源码（注释、断点调试）

## **版本选择**

目前笔者主要使用的还是 `SpringBoot 2.1.X` 以及 `SpringCloud Greenwich` 版本；我的环境如下：

* IDEA 2020.1.2
* Maven 3.6.0
* JDK 1.8
* `Nacos 1.3.1` （目前最新版）

> 操作步骤

```text
# clone 源码到本地并进入 nacos 源码目录（略）

# checkout 指定 tag
➜  git checkout tag_name

# 此时你处于头指针分离状态，只需要检出分支即可
➜  nacos git:(5e53396f) git status
HEAD detached at 5e53396f
nothing to commit, working tree clean

# 检出分支
➜  git checkout -b feature-1.3.1
➜  nacos git:(feature-1.3.1) git status
On branch feature-1.3.1
nothing to commit, working tree clean

# 关联远程分支
➜  nacos git:(feature-1.3.1) git push --set-upstream origin feature-1.3.1
Username for 'https://github.com.cnpmjs.org': rexlin600
Password for 'https://rexlin600@github.com.cnpmjs.org': 
Total 0 (delta 0), reused 0 (delta 0)
remote: 
remote: Create a pull request for 'feature-1.3.1' on GitHub by visiting:
remote:      https://github.com/rexlin600/nacos/pull/new/feature-1.3.1
remote: 
To https://github.com.cnpmjs.org/rexlin600/nacos.git
 * [new branch]        feature-1.3.1 -> feature-1.3.1
Branch 'feature-1.3.1' set up to track remote branch 'feature-1.3.1' from 'origin'.
```

