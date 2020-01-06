# mysql安装

### mac上面安装mysql

* **Mac 确保安装了brew**
* brew search mysql

![image-20200106103106154](https://tva1.sinaimg.cn/large/006tNbRwly1gammwxjlldj30uk096tg9.jpg)

 * 执行 brew install [mysql@5.7](mailto:mysql@5.7)

 * 修改.base_profile文件

   > export PATH=/usr/local/opt/mysql@5.7/bin

**mysql配置文件路径为：/usr/local/etc/my.cnf 如果该地址没有，可以在该地址下面新建一个my.cnf文件**

* 连接mysql

  > Mysql  -uroot -p

* mysql命令

  >启动命令：mysql.server start
  >
  >关闭命令：mysql.server stop
  >
  >查询状态命令： mysql.server.status

* 配置密码

   mysql_secure_installation

### linux下安装mysql

> 1. cd /usr/local/mysql .  个人习惯安装到这个目录下
>
> 2. wget http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
>
> 3. 安装rpm sudo rpm -ivh mysql57-community-release-el7-7.noarch.rpm
>
>    安装这个包后，会得到mysql的yum repo源
>
>    ![image-20200106113842102](https://tva1.sinaimg.cn/large/006tNbRwly1gamov7w91ej30wu05qtbo.jpg)
>
> 4. sudo yum install mysql-server 然后一路yes
>
> 5. 安装后应该会有个界面有密码提示，如果安装完成后 没有看到需要重置密码

**linux重制密码**

![image-20200106114315420](https://tva1.sinaimg.cn/large/006tNbRwly1gamozyqq09j31c60qiwv6.jpg)

修改密码：



> * flush privileges;
>
> * SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
>
> * GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

#### 参考链接

https://my.oschina.net/NorthIce/blog/849181