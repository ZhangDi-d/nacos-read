# Nacos 中的快照存储策略

## 回顾

前面讲 Raft 算法已经提到其中很关键的一环就是 Raft 算法会进行日志压缩，即：_**Raft 采用对整个系统进行 snapshot 来解决日志压缩，snapshot 之前的日志都可以丢弃！**_

那么这里讲的 Nacos 中的快照存储策略又是怎样的呢，请接着往下看。

## 快照存储什么数据

快照，一般会存储两类数据，分别是：①日志元数据 ②系统状态；在 Nacos 中定义了一个 LocalFileMeta 文件来描述快照存储的元数据信息，如下：

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

理解了快照的抽象接口以及读取、写入的两个快照接口类之后，下面我们来分析一下 Nacos 是如何实现快照的存储策略的





