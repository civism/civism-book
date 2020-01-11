# zookeeper节点介绍

* PERSISTENT--持久化目录节点 **客户端与zookeeper断开连接后，该节点依旧存在**
* PERSISTENT_SEQUENTIAL-持久化顺序编号目录节点 **客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号**
* EPHEMERAL-临时目录节点 **客户端与zookeeper断开连接后，该节点被删除**
* EPHEMERAL_SEQUENTIAL-临时顺序编号目录节点 **客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号**

# zookeeper分布式锁

