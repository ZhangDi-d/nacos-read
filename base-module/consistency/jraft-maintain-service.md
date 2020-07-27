# JRaft 服务管理

## 简介

{% hint style="success" %}
JRaft 服务实现具体是通过 JRaftMaintainService 实现的
{% endhint %}

Jraft 服务实现实质上就是通过 JRaftServer 获取一个 CliService 实现对 raft group 的服务管理，具体的封装体现在 JRaftOps 枚举类中。

## JRaftMaintainService

### 构造器

JRaftMaintainService 服务类维护了一个 JRaftServer，通过这个 JRaftServer 可以获取 CliService 从而实现对 raft group 的管理，JRaftMaintainService 的成员变量及构造器如下：

```java
public class JRaftMaintainService {

    private final JRaftServer raftServer;

    public JRaftMaintainService(JRaftServer raftServer) {
        this.raftServer = raftServer;
    }

}
```

### 成员方法

在 JRaftMaintainService 类中提供了 execute\(\) 方法用以执行相关命令，源码如下：

```java
    /**
     * 指向相关命令，直接返回错误
     *
     * @param args args
     * @return RestResult<String>
     */
    public RestResult<String> execute(String[] args) {
        return RestResultUtils.failed("not support yet");
    }

    /**
     * 执行相关命令
     * Execute relevant commands.
     *
     * @param args {@link Map}
     * @return {@link RestResult}
     */
    public RestResult<String> execute(Map<String, String> args) {
        // 获取 CliService
        final CliService cliService = raftServer.getCliService();

        // 如果参数中包含 groupId 则找到 Node 节点并执行 single 方法
        if (args.containsKey(JRaftConstants.GROUP_ID)) {
            final String groupId = args.get(JRaftConstants.GROUP_ID);
            final Node node = raftServer.findNodeByGroup(groupId);
            return single(cliService, groupId, node, args);
        }

        // 如果参数中不包含 groupId 则从 Multi RaftGrup 中获取一个 tupleMap
        Map<String, JRaftServer.RaftGroupTuple> tupleMap = raftServer.getMultiRaftGroup();

        // 遍历执行 single 方法，如果出现错误则直接返回 result
        for (Map.Entry<String, JRaftServer.RaftGroupTuple> entry : tupleMap.entrySet()) {
            final String group = entry.getKey();
            final Node node = entry.getValue().getNode();
            RestResult<String> result = single(cliService, group, node, args);
            if (!result.ok()) {
                return result;
            }
        }
        return RestResultUtils.success();
    }
```

第二个 execute\(\) 方法接收一个 Map&lt;String, String&gt; 类型的参数，在内部实现中逻辑如下：

1. 通过 JRaftServer 获取 CliService
2. 如果 Map 参数中包含 groupId 则路由到对应 node 节点去执行 single 方法
3. 如果 Map 参数中不包含 groupId 则先通过 RaftServer 获取 RaftGroup 元组节点 Map 并遍历执行 single 方法

对应 single\(\) 方法如下，可以看出，整个 JRaftMaintainService 类最为核心的代码实际上就是通过 command 枚举名称获取 JRaftOps ops，然后再执行 `ops.execute(cliService, groupId, node, args);` 方法：

```java
    /**
     * single 方法
     *
     * @param cliService cliService
     * @param groupId    groupId
     * @param node       node
     * @param args       args
     * @return
     */
    private RestResult<String> single(CliService cliService, String groupId, Node node, Map<String, String> args) {
        try {
            // 不存在 node
            if (node == null) {
                return RestResultUtils.failed("not this raft group : " + groupId);
            }

            // 获取 command 枚举名称
            final String command = args.get(JRaftConstants.COMMAND_NAME);

            // 重点：根据枚举获取不同的操作 command 命令
            JRaftOps ops = JRaftOps.sourceOf(command);
            if (Objects.isNull(ops)) {
                return RestResultUtils.failed("Not support command : " + command);
            }

            // 执行 command 命令并返回结果
            return ops.execute(cliService, groupId, node, args);
        } catch (Throwable ex) {
            return RestResultUtils.failed(ex.getMessage());
        }
    }
```

## JRaftOps

可以看出，整个 JRaftMaintainService 类最为核心的代码实际上就是通过 command 枚举名称获取 JRaftOps ops，然后再执行 `ops.execute(cliService, groupId, node, args);` 方法，而具体的方法实现，则在枚举类 JRaftOps 中，如下：

```java
public enum JRaftOps {

    // 转移 Leader
    TRANSFER_LEADER("transferLeader") {
        @Override
        public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
            final Configuration conf = node.getOptions().getInitialConf();
            final PeerId leader = PeerId.parsePeer(args.get(JRaftConstants.COMMAND_VALUE));
            Status status = cliService.transferLeader(groupId, conf, leader);
            if (status.isOk()) {
                return RestResultUtils.success();
            }
            return RestResultUtils.failed(status.getErrorMsg());
        }
    },

    // 重置Raft集群
    RESET_RAFT_CLUSTER("restRaftCluster") {
        @Override
        public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
            final Configuration conf = node.getOptions().getInitialConf();
            final String peerIds = args.get(JRaftConstants.COMMAND_VALUE);
            Configuration newConf = JRaftUtils.getConfiguration(peerIds);
            Status status = cliService.changePeers(groupId, conf, newConf);
            if (status.isOk()) {
                return RestResultUtils.success();
            }
            return RestResultUtils.failed(status.getErrorMsg());
        }
    },

    // 执行快照
    DO_SNAPSHOT("doSnapshot") {
        @Override
        public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
            final Configuration conf = node.getOptions().getInitialConf();
            final PeerId peerId = PeerId.parsePeer(args.get(JRaftConstants.COMMAND_VALUE));
            Status status = cliService.snapshot(groupId, peerId);
            if (status.isOk()) {
                return RestResultUtils.success();
            }
            return RestResultUtils.failed(status.getErrorMsg());
        }
    },

    // 移除一个节点
    REMOVE_PEER("removePeer") {
        @Override
        public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
            final Configuration conf = node.getOptions().getInitialConf();

            List<PeerId> peerIds = cliService.getPeers(groupId, conf);

            final PeerId waitRemove = PeerId.parsePeer(args.get(JRaftConstants.COMMAND_VALUE));

            if (!peerIds.contains(waitRemove)) {
                return RestResultUtils.success();
            }

            Status status = cliService.removePeer(groupId, conf, waitRemove);
            if (status.isOk()) {
                return RestResultUtils.success();
            }
            return RestResultUtils.failed(status.getErrorMsg());
        }
    },

    // 移除多个节点
    REMOVE_PEERS("removePeers") {
        @Override
        public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
            final Configuration conf = node.getOptions().getInitialConf();
            final String peers = args.get(JRaftConstants.COMMAND_VALUE);
            for (String s : peers.split(",")) {

                List<PeerId> peerIds = cliService.getPeers(groupId, conf);
                final PeerId waitRemove = PeerId.parsePeer(s);

                if (!peerIds.contains(waitRemove)) {
                    continue;
                }

                Status status = cliService.removePeer(groupId, conf, waitRemove);
                if (!status.isOk()) {
                    return RestResultUtils.failed(status.getErrorMsg());
                }
            }
            return RestResultUtils.success();
        }
    },

    // 修改节点信息
    CHANGE_PEERS("changePeers") {
        @Override
        public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
            final Configuration conf = node.getOptions().getInitialConf();
            final Configuration newConf = new Configuration();
            String peers = args.get(JRaftConstants.COMMAND_VALUE);
            for (String peer : peers.split(",")) {
                newConf.addPeer(PeerId.parsePeer(peer.trim()));
            }

            if (Objects.equals(conf, newConf)) {
                return RestResultUtils.success();
            }

            Status status = cliService.changePeers(groupId, conf, newConf);
            if (status.isOk()) {
                return RestResultUtils.success();
            }
            return RestResultUtils.failed(status.getErrorMsg());
        }
    };

    // -----------------------------------------------------------------------------------------------
    // 枚举类成员变量、成员方法
    // -----------------------------------------------------------------------------------------------

    /**
     * 枚举类成员变量
     */
    private String name;

    /**
     * 构造器
     *
     * @param name name
     */
    JRaftOps(String name) {
        this.name = name;
    }

    /**
     * 根据 command 名称返回枚举类
     *
     * @param command command
     * @return
     */
    public static JRaftOps sourceOf(String command) {
        for (JRaftOps enums : JRaftOps.values()) {
            if (Objects.equals(command, enums.name)) {
                return enums;
            }
        }
        return null;
    }

    /**
     * 枚举类成员执行方法
     *
     * @param cliService cliService
     * @param groupId    groupId
     * @param node       node
     * @param args       args
     * @return
     */
    public RestResult<String> execute(CliService cliService, String groupId, Node node, Map<String, String> args) {
        return RestResultUtils.success();
    }

}
```

可以看出，最终实现还是依托于 JRaft 的 CliService 实现的枚举类中定义的诸多 command

