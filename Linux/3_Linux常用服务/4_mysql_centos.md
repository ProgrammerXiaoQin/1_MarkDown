## 一.MySQL centos7 安装mysql8
1. 下载 
	- 进入www.mysql.com , 下载需要的社区版
	- 将下载的tar包上传到服务器 , 或在服务器上用`wget url`或`curl -O url`下载
2. 安装
	- 安装前用`rpm -qa|grep mariadb`检查是否有mariadb库 , 该库跟MySQL有冲突 ,如果有用`rpm -e --nodeps mariadb-libs`强制卸载掉
	- 用`tar -xvf file`解压tar包
	- 用`rpm -ivh file.rpm` 按顺序安装以下包
		1. mysql-community-common
		2. mysql-community-client-plugins
		3. mysql-community-libs
		4. mysql-community-client
		5. mysql-community-icu-data-files
		6. mysql-community-server​

3. 使用
	1. 初始化`mysqld --initialize --console`
	2. 修改安装目录所有用户和所属组 , 以便MySQL所属组用户直接使用`chown -R mysql:mysql /var/lib/mysql/`
	3. 启动服务`systemctl start mysqld`
	4. 查看默认密码`cat /var/log/mysqld.log | grep localhost`
	5. 登录`mysql -u username -p`
	6. 修改密码`alter user 'username'@'localhost' identified by 'newpass'`

4. 修改配置 , 一般在`/etc/my.cnf` ,修改过配置文件后需重新启动服务
```my.cnf
[mysqld]
# skip-grant-tables=1 跳过登录认证

default_password_lifetime=90 
#default_password_lifetime=180 设置180天过期
#default_password_lifetime=0 设置密码不过期 

#bind-address：这是MySQL服务器绑定的网络接口地址。默认情况下，它绑定到所有可用的接口。你可以将其设置为特定的IP地址，以限制服务器仅接受来自该地址的连接。

#port：MySQL服务器监听的端口号。默认端口是3306。你可以更改它，但确保相应的防火墙规则已更新以允许流量通过新端口。

#datadir：MySQL数据文件的存储目录。这是数据库文件的默认位置，可以根据需要更改。

#socket：MySQL服务器的Unix套接字文件路径。通常，客户端连接到MySQL服务器时会使用套接字文件。

#character-set-server：指定服务器的字符集。默认情况下，它是utf8，但你可以根据需要更改为其他字符集。

#collation-server：服务器的字符集校对规则。它确定如何比较和排序字符。与字符集一起使用，确保它与你的应用程序需求匹配。

#max_connections：允许的最大并发连接数。你可以根据系统资源和需求调整此值。

#keybuffersize：MyISAM存储引擎的索引缓冲区大小。如果你主要使用InnoDB存储引擎，可以将此值保持较小，或者使用innodb_buffer_pool_size来配置InnoDB缓冲池。

#innodbbufferpool_size：InnoDB存储引擎的缓冲池大小。这是用于缓存表和索引数据的重要设置，对性能有重大影响。

#log-error：指定错误日志文件的路径。这里记录了MySQL服务器的错误和警告信息。

#log-slow-queries：启用慢查询日志以记录执行时间较长的查询，有助于性能优化。

#innodblogfile_size：InnoDB日志文件的大小。这影响到事务处理和崩溃恢复。
```

## 二.MySQL centos7 安装mysql5.7
1. 下载 
	- 进入https://dev.mysql.com/downloads/mysql/ , 下载需要的版本
	- 将下载的tar包上传到服务器 , 或在服务器上用`wget url`或`curl -O url`下载
2. 安装
	- 安装前用`rpm -qa|grep mariadb`检查是否有mariadb库 , 该库跟MySQL有冲突 ,如果有用`rpm -e --nodeps mariadb-libs`强制卸载掉
	- 用`tar -xvf file`解压tar包
	- 用`rpm -ivh file.rpm` 按顺序安装以下包
		1. mysql-community-common
		2. mysql-community-libs
		3. mysql-community-libs-compat
		4. mysql-community-client
		5. mysql-community-server
3. 使用
	1. 初始化`mysqld --initialize --console`
	2. 修改安装目录所有用户和所属组 , 以便MySQL所属组用户直接使用`chown -R mysql:mysql /var/lib/mysql/`
	3. 启动服务`systemctl start mysqld`
	4. 设置开机自启动`systemctl enable mysqld`
	5. 查看默认密码`cat /var/log/mysqld.log | grep localhost`
	6. 登录`mysql -u username -p`
	7. 修改密码`alter user 'username'@'localhost' identified by 'newpass'`

## 三.常见问题
1. 链接数据库忘记密码 , 修改配置文件后执行修改密码语句报错`ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement`
	- 重启mysql服务`systemctl restart mysqld`
	- 链接数据库执行`flush privileges;` 刷新权限列表
	- 执行`alter user root@'localhost' identified by '密码';`修改密码
