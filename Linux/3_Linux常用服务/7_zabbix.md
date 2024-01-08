
#### 1. 安装
1. 关闭防火墙和selinux

2. 访问官网 `https://www.zabbix.com/cn/` , 到官网找到zabbix对应源的安装包 , 安装源 
```bash
rpm -Uvh https://repo.zabbix.com/zabbix/6.0/rhel/7/x86_64/zabbix-release-6.0-4.el7.noarch.rpm  
yum clean all
```

3. 安装zabbix服务端及数据库 , 启动mariadb
```bash
yum install -y zabbix-server-mysql zabbix-web-mysql  zabbix-agent

yum install -y mariadb-server

systemctl enable --now mariadb.service
```

4. 初始化数据库
```bash
#设置数据库初始密码
mysqladmin password "admin@123"
#登录数据库
mysql -uroot -p
#创建zabbix_proxy数据库
mysql> create database zabbix_proxy character set utf8mb4 collate utf8mb4_bin;  
#创建zabbix用户并设置密码
mysql> create user zabbix@localhost identified by 'password';  
#授予zabbix用户对于zabbix_proxy数据库最大的权限
mysql> grant all privileges on zabbix_proxy.* to zabbix@localhost;  
mysql> set global log_bin_trust_function_creators = 1;  
mysql> quit;
#导入初始架构和数据，系统将提示您输入新创建的密码
cat /usr/share/zabbix-sql-scripts/mysql/proxy.sql | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix_proxy
```
