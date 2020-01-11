# zookeeper选举模式

#### 前提：

假设有5台机器，编号为1，2，3，4，5

#### 选举分析

**zookeeper特性**

> zookeeper节点分为Leader、Follower和Observer三种角色，Leader服务器为客户端提供读和写服务，Follower和Observer都能够提供读服务，Observer机器不参与Leader选举过程，也不参与写操作的“过半写成功”策略，因此Observer可以在不影响写性能的情况下提升集群的读性能

* Leader职责：
  - 事务请求的唯一调度和处理者，保证集群事务处理的顺序性
  - 集群内部各服务器的调度者
* Follower职责：
  * 处理客户端非事务请求，转发事务请求给Leader服务器
  * 参与事务请求Proposal的投票
  * 参与Leader选举投票
* Observer职责：
  * 提供非事务服务

zookeeper Leader节点选取

> 1.zookeeper启动的时候都会默认投票给自己
>
> 2.得票超过半数后会成为Leader节点
>
> 3.没有master节点，选举编号大的会胜出 n/2+1

* 启动zk1的时候，没有超过半数3，由于默认投票给自己，所以zk1获得一票,由于没有其他机器，所以处于Looking阶段
* 启动zk2的时候，没有超过半数3，zk2得一票，跟zk1比较，zk2的myid大于zk1，所以zk2胜出，但是没有超过半数，所以处于Looking阶段
* 启动zk3的时候，zk3得一票，然后跟zk2、zk1比较，zk3胜出，超过半数3 所以称为Leader节点，1，2 更新状态为Follower
* 启动zk4的时候，由于zk3成为Leader了，所以只能成为Foller
* 启动zk5