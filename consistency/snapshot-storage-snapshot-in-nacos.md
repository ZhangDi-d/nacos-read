# Nacos 中的快照存储策略

## 回顾

前面讲 Raft 算法已经提到其中很关键的一环就是 Raft 算法会进行日志压缩，即：_**Raft 采用对整个系统进行 snapshot 来解决日志压缩，snapshot 之前的日志都可以丢弃！**_那么 Nacos 中的快照存储策略又是怎样的呢，我们需要搞清楚如下几个问题：

* Nacos 的快照存储了什么数据
* Nacos 中快照是如何保存、加载的
* 
## 快照存储什么数据

快照，一般会存储两类数据，分别是：**日志元数据、系统状态**；在 Nacos 中定义了一个 LocalFileMeta 文件来描述快照存储的元数据信息，如下：

```java
public class LocalFileMeta {

    // 元数据信息
    private final Properties fileMeta;

    // 构造器
    public LocalFileMeta() {
        this.fileMeta = new Properties();
    }

    // 构造器
    public LocalFileMeta(Properties properties) {
        this.fileMeta = properties;
    }

    /**
     * 往元数据中增加内容
     *
     * @param key   the key
     * @param value the value
     * @return local file meta
     */
    public LocalFileMeta append(Object key, Object value) {
        fileMeta.put(key, value);
        return this;
    }

    /**
     * 获取元数据
     *
     * @param key the key
     * @return object
     */
    public Object get(String key) {
        return fileMeta.getProperty(key);
    }

    /**
     * Gets file meta.
     *
     * @return file meta
     */
    public Properties getFileMeta() {
        return fileMeta;
    }
    
}
```

## 快照读取与写入

在 Nacos 中定义了一个 SnapshotOperation 用于定义快照的保存、加载，如下：

```java
public interface SnapshotOperation {

    /**
     * 保存快照
     *
     * <p>do snapshot save operation.
     *
     * @param writer      {@link Writer}
     * @param callFinally Callback {@link BiConsumer} when the snapshot operation is complete
     */
    void onSnapshotSave(Writer writer, BiConsumer<Boolean, Throwable> callFinally);

    /**
     * 加载快照
     *
     * <p>do snapshot load operation.
     *
     * @param reader {@link Reader}
     * @return operation label
     */
    boolean onSnapshotLoad(Reader reader);
}
```

可以看到，保存时需要传入一个 Writer 对象以及一个快照写入完成的回调函数式接口；加载快照时需要传入一个 Reader 对象。下面我们先来看下 Rader、Writer 对象是什么：

### Writer 对象

```java
public class Writer {

    /**
     * 要写入的数据 Map，key为文件名称、 value为文件元数据
     */
    private final Map<String, LocalFileMeta> files = new HashMap<>();

    /**
     * 写入的路径
     */
    private String path;

    /**
     * 构造器
     *
     * @param path path
     */
    public Writer(String path) {
        this.path = path;
    }

    /**
     * 返回快照文件路径
     *
     * @return String
     */
    public String getPath() {
        return path;
    }

    /**
     * 添加不带元数据信息的快照
     * Adds a snapshot file without metadata.
     *
     * @param fileName file name
     * @return true on success
     */
    public boolean addFile(final String fileName) {
        files.put(fileName, new LocalFileMeta().append("file-name", fileName));
        return true;
    }

    /**
     * 添加带元数据信息的快照
     * Adds a snapshot file with metadata.
     *
     * @param fileName file name
     * @return true on success
     */
    public boolean addFile(final String fileName, final LocalFileMeta meta) {
        files.put(fileName, meta);
        return true;
    }

    /**
     * 移除快照文件
     * Remove a snapshot file.
     *
     * @param fileName file name
     * @return true on success
     */
    public boolean removeFile(final String fileName) {
        files.remove(fileName);
        return true;
    }

    /**
     * List files map.
     *
     * @return map
     */
    public Map<String, LocalFileMeta> listFiles() {
        return Collections.unmodifiableMap(files);
    }

}
```

### Reader 对象

```java
public class Reader {

    /**
     * 读取路径
     */
    private final String path;

    /**
     * 读取的所有快照 Map
     */
    private final Map<String, LocalFileMeta> allFiles;

    /**
     * 构造器
     *
     * @param path     the path
     * @param allFiles the all files
     */
    public Reader(String path, Map<String, LocalFileMeta> allFiles) {
        this.path = path;
        this.allFiles = Collections.unmodifiableMap(allFiles);
    }

    /**
     * Gets path.
     *
     * @return the path
     */
    public String getPath() {
        return path;
    }


    /**
     * List files map.
     *
     * @return the map
     */
    public Map<String, LocalFileMeta> listFiles() {
        return allFiles;
    }

    /**
     * 根据文件名称读取快照元数据
     *
     * @param fileName the file name
     * @return file meta
     */
    public LocalFileMeta getFileMeta(String fileName) {
        return allFiles.get(fileName);
    }
}
```

从上面的源码可以分析得出如下结论：

* Reader 为封装的读取快照的类
* Writer 为封装的写入快照的类
* SnapshotOperation 抽象了快照的保存、加载

理解了快照的抽象接口以及读取、写入的两个快照接口类之后，下面我们来分析一下 Nacos 是如何实现快照的存储策略的（这里介绍 Nacos 使用 Derby 实现的快照存储策略）

## Derby 快照操作实现

从源码中我们可以看出 DerbySnapshotOperation 这个类实现了 SnapshotOperation 接口，底层依赖于 Derby 数据库完成对快照数据的保存、加载。换句话说，DerbySnapshotOperation 这个类相当于是一个工具类，用于保存、加载快照。

DerbySnapshotOperation 内部维护了快照目录名称、快照存档名称、Derby 数据库连接地址、可重入读写锁写锁等内部成员变量，并实现了保存、加载快照方法，如下：

```java
    /**
     * 快照存储
     *
     * @param writer      {@link Writer}
     * @param callFinally Callback {@link BiConsumer} when the snapshot operation is complete
     */
    @Override
    public void onSnapshotSave(Writer writer, BiConsumer<Boolean, Throwable> callFinally) {
        // 启动一个线程执行快照保存
        RaftExecutor.doSnapshot(() -> {

            // 快照存储开始时间
            TimerContext.start("CONFIG_DERBY_SNAPSHOT_SAVE");

            // 获得锁
            final Lock lock = writeLock;
            lock.lock();
            try {
                // 获取写入快照路径
                final String writePath = writer.getPath();
                final String parentPath = Paths.get(writePath, snapshotDir).toString();
                // 删除目录、创建目录
                DiskUtils.deleteDirectory(parentPath);
                DiskUtils.forceMkdir(parentPath);

                // 执行备份操作
                doDerbyBackup(parentPath);

                // 归档目录
                final String outputFile = Paths.get(writePath, snapshotArchive).toString();
                final Checksum checksum = new CRC64();
                // 压缩
                DiskUtils.compress(writePath, snapshotDir, outputFile, checksum);
                // 删除目录
                DiskUtils.deleteDirectory(parentPath);

                // 元数据存入校验码
                final LocalFileMeta meta = new LocalFileMeta();
                meta.append(checkSumKey, Long.toHexString(checksum.getValue()));

                // writer 增加归档文件、元数据
                callFinally.accept(writer.addFile(snapshotArchive, meta), null);
            } catch (Throwable t) {
                LogUtil.FATAL_LOG.error("Fail to compress snapshot, path={}, file list={}, {}.", writer.getPath(),
                    writer.listFiles(), t);
                callFinally.accept(false, t);
            } finally {
                // 释放锁
                lock.unlock();
                // 计时器结束
                TimerContext.end(LogUtil.FATAL_LOG);
            }
        });
    }

    /**
     * 加载快照
     *
     * @param reader {@link Reader}
     * @return
     */
    @Override
    public boolean onSnapshotLoad(Reader reader) {
        // 获取读取目录
        final String readerPath = reader.getPath();
        final String sourceFile = Paths.get(readerPath, snapshotArchive).toString();

        // 开始计时、获取写入所
        TimerContext.start("CONFIG_DERBY_SNAPSHOT_LOAD");
        final Lock lock = writeLock;
        lock.lock();
        try {
            // 解压
            final Checksum checksum = new CRC64();
            DiskUtils.decompress(sourceFile, readerPath, checksum);

            // 从归档文件读取元数据信息
            LocalFileMeta fileMeta = reader.getFileMeta(snapshotArchive);

            // CRC64校验
            if (fileMeta.getFileMeta().containsKey(checkSumKey)) {
                if (!Objects.equals(Long.toHexString(checksum.getValue()), fileMeta.get(checkSumKey))) {
                    throw new IllegalArgumentException("Snapshot checksum failed");
                }
            }

            final String loadPath = Paths.get(readerPath, snapshotDir, "derby-data").toString();
            LogUtil.FATAL_LOG.info("snapshot load from : {}, and copy to : {}", loadPath, derbyBaseDir);

            // 从快照中恢复
            doDerbyRestoreFromBackup(() -> {
                final File srcDir = new File(loadPath);
                final File destDir = new File(derbyBaseDir);

                DiskUtils.copyDirectory(srcDir, destDir);
                LogUtil.FATAL_LOG.info("Complete database recovery");
                return null;
            });

            // 删除目录
            DiskUtils.deleteDirectory(loadPath);

            // 发布 Derby 事件
            NotifyCenter.publishEvent(DerbyLoadEvent.INSTANCE);
            return true;
        } catch (final Throwable t) {
            LogUtil.FATAL_LOG
                .error("Fail to load snapshot, path={}, file list={}, {}.", readerPath, reader.listFiles(), t);
            return false;
        } finally {
            // 解锁、计时结束
            lock.unlock();
            TimerContext.end(LogUtil.FATAL_LOG);
        }
    }
    
```

可以看出，最终实现保存、备份是基于 doDerbyBackup\(\)、doDerbyRestoreFromBackup\(\) 这两个方法，如下：

```java
    /**
     * 执行 Derby 备份
     *
     * @param backupDirectory the backupDirectory
     * @throws Exception exception
     */
    private void doDerbyBackup(String backupDirectory) throws Exception {
        // 获取动态数据源
        DataSourceService sourceService = DynamicDataSource.getInstance().getDataSource();
        DataSource dataSource = sourceService.getJdbcTemplate().getDataSource();
        // 执行备份
        try (Connection holder = Objects.requireNonNull(dataSource, "dataSource").getConnection()) {
            CallableStatement cs = holder.prepareCall(backupSql);
            cs.setString(1, backupDirectory);
            cs.execute();
        }
    }

    /**
     * 执行 Derby 从备份中恢复
     *
     * @param callable the callable
     * @throws Exception exception
     */
    private void doDerbyRestoreFromBackup(Callable<Void> callable) throws Exception {
        // 获取动态数据源
        DataSourceService sourceService = DynamicDataSource.getInstance().getDataSource();
        // 强转为 LocalDataSourceServiceImpl
        LocalDataSourceServiceImpl localDataSourceService = (LocalDataSourceServiceImpl) sourceService;
        // 从备份中恢复
        localDataSourceService.restoreDerby(restoreDB, callable);
    }
```

保存快照时，先获取动态数据源（单机模式默认使用 Embedded storage、集群模式默认使用 external databases）再建立数据库连接，接着注入 backupSql 后执行。

加载快照时，同样的先获取动态数据源并强转为 LocalDataSourceServiceImpl，最后执行  LocalDataSourceServiceImpl.restoreDerby\(\) 方法实现 Derby 数据的加载，如下：

```java
    /**
     * Restore derby.
     *
     * @param jdbcUrl jdbcUrl string value.
     * @param callable callable.
     * @throws Exception exception.
     */
    public void restoreDerby(String jdbcUrl, Callable<Void> callable) throws Exception {
        // 清空 Derby 目录
        doDerbyClean();
        callable.call();
        // 初始化连接并加载 Derby
        initialize(jdbcUrl);
    }
    
    /**
     * 初始化
     *
     * @param jdbcUrl the jdbcUrl
     */
    private synchronized void initialize(String jdbcUrl) {
        HikariDataSource ds = new HikariDataSource();
        ds.setDriverClassName(jdbcDriverName);
        ds.setJdbcUrl(jdbcUrl);
        ds.setUsername(userName);
        ds.setPassword(password);
        ds.setIdleTimeout(30_000L);
        ds.setMaximumPoolSize(80);
        ds.setConnectionTimeout(10000L);
        DataSourceTransactionManager tm = new DataSourceTransactionManager();
        tm.setDataSource(ds);
        if (jdbcTemplateInit) {
            jt.setDataSource(ds);
            tjt.setTransactionManager(tm);
        } else {
            jt = new JdbcTemplate();
            jt.setMaxRows(50000);
            jt.setQueryTimeout(5000);
            jt.setDataSource(ds);
            tjt = new TransactionTemplate(tm);
            tjt.setTimeout(5000);
            jdbcTemplateInit = true;
        }
        // 重载
        reload();
    }
    
    @Override
    public synchronized void reload() {
        // 获取数据源
        DataSource ds = jt.getDataSource();
        if (ds == null) {
            throw new RuntimeException("datasource is null");
        }
        try {
            // 建立连接并执行 SQL
            execute(ds.getConnection(), "META-INF/schema.sql");
        } catch (Exception e) {
            if (LogUtil.DEFAULT_LOG.isErrorEnabled()) {
                LogUtil.DEFAULT_LOG.error(e.getMessage(), e);
            }
            throw new NacosRuntimeException(NacosException.SERVER_ERROR, "load schema.sql error.", e);
        }
    }
```









