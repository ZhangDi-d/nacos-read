# Apache Derby

## 简介

Apache Derby 是一个完全用 Java 开发的数据库，是一款开源产品。Apache Derby 的核心亮点就是小巧，既可以单独作为数据库服务器使用，也可以内嵌在应用程序中使用。

从 JDK 1.6 开始，Derby 就已经被纳入了 JDK 中，你可以从你安装的 JDK 目录下的 db 文件夹找到它！

## 配置 Derby

因为 Derby 是基于 Java 编写的，因此其需要依托于 JRE，这里就不单独介绍 JDK 的安装了。其实配置 Derby 非常的简单，只需要配置系统环境变量即可，如下：

```yaml
# 1. 增加 DERBY_HOME（指定系统环境变量 name=DERBY_HOME value=<你的 derby 位置>）

# 2. Path 配置中添加如下信息
%DERBY_HOME%\bin

# 3. CLASSPATH 配置中添加如下信息
%DERBY_HOME%\lib \derby.jar;%DERBY_HOME%\lib\derbyclient.jar;%DERBY_HOME%\lib\derbytools.jar;%DERBY_HOME%\lib\derbynet.jar
```

配置完成之后，可以在控制台输入 sysinfo，然后看到如下信息：

```bash
PS C:\Windows\system32> sysinfo
------------------ Java 信息 ------------------
Java 版本：        1.8.0_171
Java 供应商：      Oracle Corporation
Java 主目录：      C:\Program Files\Java\jdk1.8.0_171\jre
Java 类路径：      .;C:\Program Files\Java\jdk1.8.0_171\lib;C:\Program Files\Java\jdk1.8.0_171\lib\tools.jar;D:\Derby\li                                                                                                                                                       b \derby.jar;D:\Derby\lib\derbyclient.jar;D:\Derby\lib\derbytools.jar;D:\Derby\lib\derbynet.jar;D:\Derby/lib/derby.jar;D                                                                                                                                                       :\Derby/lib/derbynet.jar;D:\Derby/lib/derbyclient.jar;D:\Derby/lib/derbytools.jar;D:\Derby/lib/derbyoptionaltools.jar
OS 名：            Windows 10
OS 体系结构：      amd64
OS 版本：          10.0
Java 用户名：      hekunlin
Java 用户主目录：C:\Users\hekunlin
Java 用户目录：    C:\Windows\system32
java.specification.name: Java Platform API Specification
java.specification.version: 1.8
java.runtime.version: 1.8.0_171-b11
--------- Derby 信息 --------
[D:\Derby\lib\derby.jar] 10.14.2.0 - (1828579)
[D:\Derby\lib\derbytools.jar] 10.14.2.0 - (1828579)
[D:\Derby\lib\derbynet.jar] 10.14.2.0 - (1828579)
[D:\Derby\lib\derbyclient.jar] 10.14.2.0 - (1828579)
[D:\Derby\lib\derbyoptionaltools.jar] 10.14.2.0 - (1828579)
------------------------------------------------------
----------------- 区域设置信息 -----------------
当前区域设置：  [中文/中国 [zh_CN]]
找到支持的区域设置：[cs]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[de_DE]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[es]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[fr]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[hu]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[it]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[ja_JP]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[ko_KR]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[pl]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[pt_BR]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[ru]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[zh_CN]
         版本：10.14.2.0 - (1828579)
找到支持的区域设置：[zh_TW]
         版本：10.14.2.0 - (1828579)
------------------------------------------------------
------------------------------------------------------
PS C:\Windows\system32>
```

## Derby 操作

> 创建数据库并连接（存在则连接、不存在则创建后为连接）

```bash
# 内嵌模式
connect 'jdbc:derby:dedb;user=db_user1;password=111111;create=true'; 

# 服务器模式
connect 'jdbc:derby://127.0.0.1:1527/debryDB;user=db_user1;password=111111;create=true';
```

> 表、数据操作

```sql
-- 创建表
create table t_user(uuid varchar(32), name varchar(10), age int, address varchar(40));

-- 插入数据
insert into t_user values('B82A6C5244244B9BB226EF31D5CBE508', 'Miachel', 20, 'street 1');
insert into t_user values('B82A6C5244244B9BB226EF31D5CBE509', 'Andrew', 35, 'street 1');
insert into t_user values('B82A6C5244244B9BB226EF31D5CBE510', 'Orson', 47, 'street 1');
insert into t_user values('B82A6C5244244B9BB226EF31D5CBE511', 'Rambo', 19, 'street 1');
```

创建数据库的路径取决于你目前使用的 CMD 路径。比如你当前路径为 C:\User\Administrator 下，那么创建的 Derby 数据库就在该目录下

## Reference

* [官网下载地址](http://db.apache.org/derby/derby_downloads.html)
* [官网文档地址](https://builds.apache.org/job/Derby-docs/lastSuccessfulBuild/artifact/trunk/out/getstart/index.html)



