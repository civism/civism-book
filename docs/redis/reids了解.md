# 一、redis参数配置说明

* 项目环境说明：springboot

* redis参数配置：

  * 单机模式

    ```java
    #redis服务器地址
    spring.redis.host=
    #redis端口号
    spring.redis.port=
    #使用数据库的索引编号（默认为0）
    spring.redis.database=0
    #redis超时时间  
    spring.redis.timeout=1000
    #是否启用SSL连接
    spring.redis.ssl=false
    #redis密码
    spring.redis.password=
    ```

  * 集群模式

    * jedis链接模式:Jedis 是直连模式，在多个线程间共享一个 Jedis 实例时是线程不安全的，如果想要在多线程环境下使用 Jedis，需要使用连接池,每个线程都去拿自己的 Jedis 实例，当连接数量增多时，物理连接成本就较高了。

      ```java
      #redis超时时间  
      spring.redis.timeout=1000
      #是否启用SSL连接
      spring.redis.ssl=false
      #redis密码
      spring.redis.password=
      #集群模式下，逗号分隔的键值对（主机：端口）形式的服务器列表
      spring.redis.cluster.nodes=
      #集群模式下，集群最大转发的数量
      spring.redis.cluster.max-redirects=3
      #最小空闲连接数量
      spring.redis.jedis.pool.min-idle=2
      #连接池最大阻塞等待时间,使用负值表示没有限制
      spring.redis.jedis.pool.max-wait=200
      #最小空闲连接数量，使用正值才有效果
      spring.redis.jedis.pool.max-idle=8
      #连接池的最大活动连接数量，使用负值无限制
      spring.redis.jedis.pool.max-active=8
      #空闲链接检测线程检测周期
      spring.redis.jedis.pool.time-between-eviction-runs=
      ```

    * lettuce链接模式: Lettuce的连接是基于Netty的，连接实例可以在多个线程间共享(springboot开始使用了本模式)

      ```
      #redis超时时间  
      spring.redis.timeout=
      #是否启用SSL连接
      spring.redis.ssl=false
      #redis密码
      spring.redis.password=
      #集群模式下，逗号分隔的键值对（主机：端口）形式的服务器列表
      spring.redis.cluster.nodes=
      spring.redis.cluster.max-redirects=
      #连接池最大连接数（使用负值表示没有限制）
      spring.redis.lettuce.pool.max-active=8
      #连接池中的最大空闲连
      spring.redis.lettuce.pool.max-idle=8
      #连接池最大阻塞等待时间,使用负值表示没有限制
      spring.redis.lettuce.pool.max-wait=
      #连接池中的最小空闲连接
      spring.redis.lettuce.pool.min-idle=
      spring.redis.lettuce.pool.time-between-eviction-runs=
      #关闭超时时间
      spring.redis.lettuce.shutdown-timeout=
      ```

      

  #  二、redis实战

  # 三、线上事故回顾

  2019年12月27日早晨9点左右，技术反应APP生产环境无法访问。后来发现包括app和小程序等各端都无法进行正常的流程，业务基本处于全线崩溃状态。

  **事故排查：**

  ​	**9点半左右**，排查redis状态，发现集群正常(**此时主要排查的是集群的存活状态，可能方式不是很合适，得出错误判断**)。

  ​	**10点左右**，初步怀疑是服务redis的连接池过大，占满了redis集群的连接池。同时发现业务报错如下：

  ```
  Caused by: org.redisson.client.RedisException: ERR max number of clients reached. channel: 
  于是，重新发布主要业务，但是状态依旧如此。
  2019-12-27 10:16:40.612 [] [] [redisson-netty-5-16] ERROR o.r.cluster.ClusterConnectionManager 206 - Can't connect to master: [redis://192.168.1.1:17001](redis://192.168.1.2:17001)
  with slot ranges: [[10923-16383]]
  ```

  ​	**10:30**，主要开发和运维集中在小会议室重点排查。

  ​	**10:35**，查看redis的启动日志，发现如下报错：

  ```
  Server can't set maximum open files to 10032 because of OS error: Operation not permitted
  ```

    						初步怀疑redis的最大客户端连接数配置没有生效。

  ​	**10:48左右** 发现redis集群第3台的redis连接数过大，重启了其中某台redis集群节点,释放了连接数。

   	重启之后观察，业务恢复正常

  **事故分析：**

   生产redis集群的maxclients已经配置为10000，但是我们用的redis用户启动服务，该配置没有生效，但是服务可以正常运行，且日志级别为warning,同时maxclients参数被强制设置为4096。目前查询资料表明，redis只有root用户启动，系统才会加载这个配置,因为该配置会调整系统参数。

  **事故影响：**

   线上业务全线崩盘，直到10:46才恢复。

  **事故解决：**

   (1).调整了生产环境redis集群该参数，目前已经调整为10000；

  (2).调整业务端连接池参数。

   (3).增强告警机制和渠道。目前生产环境只有邮件告警生效，钉钉告警只接入了部分。其实redis集群的告警昨晚已经发送到了邮箱，但是没有及时查看邮件，所有最后直接导致了业务故障。

