###### 1. 修改配置
1. 修改密码 , 登录进mysql后 , 执行`set password = password("root123");`
2. 忘记密码:
	- 在配置文件中加入`skip-grant-tables=1`
	- 执行 `use mysql; alter user 'username'@'%' identified by 'newpass';` 修改密码
	- 退出mysql删除配置文件中的`skip-grant-tables=1` , 重启mysql服务
```my.cnf
[mysqld]
...
skip-grant-tables=1
...
```

###### 2. 数据库
1. 登录 `mysql -u username -p`

2. 查看所有数据库 `show databases;`

3. 创建数据库
	- `create database 数据库名`  创建数据库
	- `create database 数据库名 default charset=utf8 collate=utf8_unicode_ci;` 创建编码为uft-8的数据库

4. 删除数据库 `drop database 数据库名;`

5. 进入数据库 `use 数据库名;`

###### 3. 表 , 进入数据库后可进行对表的操作
1. 查看所有表 `show tables;`

2. 创建表
	- 列名 类型后面加 `not null (不允许为空)`  `null(允许为空 默认)`
	- 列名 类型后面加`default ..` 设置默认值
	- 列名 类型后面加`primary key` 设置该列为主键(不允许为空, 不能重复)
	- 列名 类型后面加`auto_increment` 设置自增 , 后面接`primary key`可以设置自增主键
```mysql
create table 表名 (
	id int primary key,         
	name varchar(16) not null, 
	email varchar(32) null,
	age int default 3
) default charset=utf8;
```

```mysql
create table day01 (
	id int not null auto_increment primary key,
	name varchar(16) not null,
	email varchar,
	age int default 3
)
```

3. 删除表:  `drop table 表名;` 清除表(删除表的数据 , 不删除表): `delete from 表名;` 或 `truncate table 表名;`(速度快, 但是无法回滚,撤销)

4. 添加列  : `alter table 表名 add 列名 类型;` (这里也可以跟创建表时的操作)

5. 修改列 : `alter table 表名 modify column 列名 类型;`