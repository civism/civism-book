# mysql主从复制



* 修改mysql简单密码 。

  ```mysql
  SET PASSWORD FOR 'root'@'localhost' = PASSWORD ('Guava123#'); 
  ```

* 创建slave连接的用户账号密码

  ```mysql
  create user 'guavaslave'@'IP' IDENTIFIED BY '123456';
  ```

* 授权服务

  ```mysql
  GRANT REPLICATION SLAVE,RELOAD,SUPER ON *.*  TO 'masterslave'@'IP'  IDENTIFIED BY 'Guava123#';
  ```

  

* 修改配置文件

  > vi /etc/my.conf

* 主从配置

  > server_id=1
  >
  > log-bin=mysql-bin

* 重启mysql服务

  > service mysqld restart

* 连接mysql数据库

  > show master status;

**从库**

* 服务器的ID,必须唯一，一般设置自己的IP

  > server_id=2

* 复制过滤：不需要备份的数据库（MySQL库一般不同步）

  > binlog-ignore-db=mysql

* 开启二进制日志功能，名字可以随便取，最好有含义（比如项目名）

  > log-bin=guava-slave

* 为每个 session 分配的内存,在事务过程中用来存储二进制日志的缓存

  > binlog_cache_size=1M

* 主从复制的格式(mixed,statement,row,默认格式是 statement)

  > binlog_format=mixed

* 二进制日志自动删除/过期的天数。默认值为 0,表示不自动删除。

  > expire_logs_days=3

* 跳过主从复制中遇到的所有错误或指定类型的错误,避免 slave 端复制中断。

  > 如:1062 错误是指一些主键重复,1032 错误是因为主从数据库数据不一致
  >
  > slave_skip_errors=1062

* 作为从服务器时的中继日志

  > relay_log=edu-mysql-relay-bin

* log_slave_updates 表示 slave 将复制事件写进自己的二进制日志

  > log_slave_updates=1

* 主键自增规则，避免主从同步ID重复的问题

  > auto_increment_increment=2  # 自增因子（每次加2）
  >
  > auto_increment_offset=2     # 自增偏移（从1开始），单数



* 连接mysql

  ```mysql
  CHANGE MASTER TO MASTER_HOST='IP', MASTER_USER='guavaslave',MASTER_PASSWORD='Guava123#',MASTER_LOG_FILE='mysql-bin.000001',MASTER_LOG_POS=0;
  ```

  

* 查看状态

   >Slave_IO_Running=Yes
   >
   >Slave_SQL_Running=Yes

* 开始同步

   >START SLAVE

# 参考链接

https://my.oschina.net/u/2988360/blog/887179