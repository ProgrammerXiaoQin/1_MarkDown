## 二.MySQL centos7 安装mysql
1. 下载 
	- 进入www.mysql.com , 下载需要的社区版
	- 将下载的tar包上传到服务器 , 或在服务器上用`wget url`或`curl -O url`下载
2. 安装
	- 安装前用`rpm -qa|grep mariadb`检查是否有mariadb库 , 该库跟MySQL有冲突 ,如果有用`rpm -e --nodeps maria-libs`强制卸载掉
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

4. 修改配置 , 一般在`/etc/my.cnf`
```my.cnf
[mysqld]
# skip-grant-tables=1 跳过登录认证


default_password_lifetime=90 
#default_password_lifetime=180 设置180天过期
#default_password_lifetime=0 设置密码不过期 

```