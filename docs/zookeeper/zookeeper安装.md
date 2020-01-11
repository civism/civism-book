# zookeeper安装

### 1.zookeeper单机安装

* 下载zookeeper并解压

  > wget  http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.5.6/apache-zookeeper-3.5.6.tar.gz
  >
  > tar -zxf apache-zookeeper-3.5.6.tar.gz 

* 配置zk

  > cd conf	
  >
  > cp zoo_sample.cfg  zoo.cfg 

*  启动zk

  > cd bin
  >
  > ./zkServer.sh start

### 2.zookeeper集群安装

* 修改zoo.conf文件

  > server.1=xxx.xxx.xxx.xxx:2888:3888
  >
  >  server.2=xxx.xxx.xxx.xxx:2888:3888
  >
  >  server.3=xxx.xxx.xxx.xxx:2888:3888

* 查看zoo.conf中配置文件信息

  > dataDir=/tmp/zookeeper **可以修改路径**
  >
  > 在dataDir中创建myid文件：touch myid
  >
  > 分别写入编号： echo '1'>>myid     echo '2'>>myid   echo '3'>>myid

### 3.zookeeper启动命令

```
#启动ZK服务: 
./zkServer.sh start
#停止ZK服务: 
./zkServer.sh stop
#重启ZK服务: 
./zkServer.sh restart
#查看ZK服务状态: 
./zkServer.sh status
```

