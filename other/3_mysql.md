### 1. 修改配置
1. 修改当前登录用户密码 , 登录进mysql后 , 执行`set password = password("root123");`
	- 其他用户 : `set password for 'username'@'host' = password('123456')` ;
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

3. 创建用户 `CREATE USER 'username'@'host' IDENTIFIED BY 'password';`
	- username：你将创建的用户名
	- host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，如果想让该用户可以从任意远程主机登陆，可以使用通配符`%`
	- password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器
4. 授权 `GRANT privileges ON databasename.tablename TO 'username'@'host'`
	- privileges : 用户的操作权限，如`SELECT`，`INSERT`，`UPDATE`等，如果要授予所的权限则使用`ALL`
	- databasename : 数据库名
	- tablename: 表名，如果要授予该用户对所有数据库和表的相应操作权限则可用`*`表示，如`*.*`
5. 撤销用户权限 `REVOKE privilege ON databasename.tablename FROM 'username'@'host';`
	- privilege, databasename, tablename: 同授权部分

### 2. 数据库
1. 登录 `mysql -u username -p`

2. 查看所有数据库 `show databases;`

3. 创建数据库
	- `create database 数据库名`  创建数据库
	- `create database 数据库名 default charset=utf8 collate=utf8_unicode_ci;` 创建编码为uft-8的数据库

4. 删除数据库 `drop database 数据库名;`

5. 进入数据库 `use 数据库名;`

6. 查看数据库信息 `\s` 
### 3. 表 , 进入数据库后可进行对表的操作
	或者使用`数据库名.表名`这样的格式
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

3. 删除表:  `drop table 表名;` 

4. 清空表(删除表的数据 , 不删除表): `delete from 表名;` 或 `truncate table 表名;`(速度快, 但是无法回滚,撤销)

5. 修改表
	- 添加列  : `alter table 表名 add 列名 类型;` (这里也可以跟创建表时的操作)
	- 修改列类型 : `alter table 表名 modify column 列名 类型;`
	- 删除列: `alter table 表名 drop column 列名;`
	- 修改列类型和名称: `alter table 表名 change column 原列名 新列名 类型;`
	- 修改列默认值 : `alter table 表名 alter 列名 set default 默认值;`
	- 删除列默认值 : `alter table 表名 alter 列名 drop default;`
	- 添加主键 : `alter table 表名 add primary key(列名);`
	- 删除主键 : `alter table 表名 drop primary key;`
	

6. 查看表信息 : `desc 表名;`


### 4.数据类型
1. 整数 `int[(m)][unsigned][zerofill]`
	- `int` 有符号,取值范围-2147483648~2147483647
	- `int unsigned` 无符号 , 取值范围0~4294967295
	- `int(5) zerofill` 仅用于显示 , 当不满住5位时左边补0,列:插入2 ,显示00002.


##### 5.修改和查询数据
1. 修改数据
	- 修改数据 : `update 表名 set 列名 = 数据 where id =3 ;`(这里id = 3是条件) 
	- 删除数据 : `delete from 表名 where id = 1;` (whiere后面跟条件)
	- 插入数据: `insert into 表名(列1, 列2, 列n) values (val1, val2, valn);`

2. 查找数据
	- 查找所有数据 : `select * from 表名;`
	- 查看指定列数据 : `select 列名1,列名2 from 表名;`
	- 查看指定列数据并设置别名 : `select 列名1 别名1,列名2 别名2 from 表名;`

3. 关键字
	1. `distinct` 去重
		- 该关键字要放到`select` 后
		- 例 : `select distinct Continent from Covid_month;`
		- 展示Covid_month表中Continent列中所有不同值
	2. `order by` 排序
		- 该关键字要放到的表名后
		- `order by 列名 ASC`  默认规则,从小到大
		- `order by 列名 DESC` 从大到小
		- 例 : `select * from Covid_month order by Confirmed DESC;`
		- 查看Covid表所有数据 , 并根据Confirmed列从大到小排序
	3. `where` 条件
		- 该关键字要放到表名后 , `order by`关键字之前
		- 该关键字要配合运算符使用
		- 例 : `select * from Covid_month where NOT Recovered <=1000000 AND Country = 'Brazil' order by Confirmed desc;`
		- 查询Covid_month表中 Recovered值大于1000000并且Country值为Brazil的列按照 Confirmed列的值从大到小排序
	4. `inner join 表2 on 表1.列1 = 表2.列2` 合并 , 交集
		- 该关键字要放到表名后
		- 例 : `select * from A inner join B on A.Country = B.Country;`
		- 以A,B表中共有的Country列中每个表数据相同的列合并到一起 , from后的表在左边, join后的表在右边
	5. `union` 拼接两个表数据 , 并集
		- `union` 不会有重复值 , `union all` 可能会有重复值(不会去重)
		- 例 : `select * from A union select * from B;`
		- 查询表A,B的所有数据并把他们上下拼接在一起
	6. `left join` 左连接
		- 用法类似 `inner join` 保留左边所选取的数据 , 然后把右边表格符合条件的合并过来 
		- 例 : `s`

4. 运算符 , 比较运算符 , 逻辑运算符
	- 例 : `select * from 表名 where 列名 between 10 and 20;`
	- 查询表名中指定列的值在10~20之间的所有行
	- 例 : `select * from 表名 where 列名 in ('aa' , 'bb');`
	- 查询表名中指定列的值为aa或bb的所有行
	- 例 : `select * from 表名 where 列名 like 'B%';`
	- 查询表名中指定列的值以B开头的所有行 , `%` 任意数量的任意字符 , `_`表示一个任意字符, `__b%` 就表示第三个字符为b的任意字符串
	
| 等于 | 不等于 | 大于 | 小于 | 大于等于 | 小于等于 | 两值之间 | 一值之间 | 相似匹配 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
|=| !=或 <> | > | < | >= | <= | BETWEEN | IN | LIKE |

| 与 | 或 | 非 |
| -- | -- | -- |
| AND | OR | NOT或! |

1. 查看表所有数据 : `select *  from 表名`


2. 查询内容
```mysql
select *
	字段1 别名1, 字段2 别名2, 字段n, 别名n
	distinct(去重)
	count(字段列表),max(),min(),avg(),sum()
# select后面可以跟两种内容,分别是字段名称和函数
```

### 5.权限管理
1. 权限授权表 : mysql数据库下user、db、tables_priv、columns_priv、proce_priv、proxies_priv
	- user表 : 指定全局权限 , 包括可以链接的服务器和密码
	- db表 : 指定数据库级权限 , 里指定的权限适用于一个数据库中的所有表。
	- tables_priv表 : 指定表级权限，在这里指定的一个权限适用于一个表的所有列。
	- columns_priv表 : 指定列级权限。这里指定的权限适用于一个表的特定列。
	- proce_priv表 : 指定存储过程权限。这里代表允许使用某个存储过程的权限。
	- proxies_priv : 利用 MySQL proxies_priv（模拟角色）实现类似用户组管理。角色(Role)可以用来批量管理用户，同一个角色下的用户，拥有相同的权限

2. 权限检查顺序
	1. 校验user表，对于全局权限是ok → 直接执行
	2. 检验DB表，对于某个有特定的数据库有权限 → 执行
	3. 检验tables_priv，对于特定数据库下的某些表是有权限 → 执行
	4. 检验columns_priv，对于特定表中的某些列有权限 → 执行
![[v2-1d74866e133fff3771b97ac43ffd64cd_720w.webp]]

3. 创建用户 `create user [用户名]@[访问地址] identified by [密码]`
	- `[访问地址]` 的值如果为`localhost` 表示只能本地登录 , 无法远程链接 
	- `%` 表示任何主机都可以链接

4. 授予权限 `grant [权限1,权限2,权限3] on *.* to user@'host' identified by 'password'`
	- 常用权限all privileges、create、drop、select、insert、delete、update
		- all privileges 表示将所有权限授予给用户 , 可简写为`all`
	- on : 这些权限对哪些数据库和表生效 , 格式`数据库名.表名` 这里`*`表示所有数据库所有表
	- to : 将权限授予用户。格式：”用户名”@”登录IP或域名”。%表示没有限制，在任何主机都可以登录。比如：'zhangsan'@'192.168.1.%'，表示zhangsan这个用户只能在192.168.0.* IP段登录
	- `with grant option` : 通过在grant语句的最后使用该子句，就允许被授权的用户把得到的权限继续授给其它用户
	- 注 : 使用GRANT添加权限，权限会自动叠加，不会覆盖之前授予的权限

5. 授予库 , 表权限
	- `grant select on test.* to zhangsan;`
	- 授予用户zhangsan可以对test数据库中的所有表进行查询
	- `grant create、alter、drop、create view on test.* to lisi;`
	- 授予lisi在test数据库中创建、修改、删除表的权限以及创建视图的权限
	- `grant select on * to lisi;` 
	- 授予lisi可以对当前数据库中的所有表进行查询, * :表示当前数据库
	- `grant create,alter,drop on . to lisi；`
	- 授予lisi可以创建、修改、删除数据库以及对所有数据库中的所有表进行create、alter和drop
	- `grant create user on . to lisi;` 
	- 授予lisi可以创建新用户

6.  授予列权限
	- `grant select(id,name,price) on test.temp to zhangsan@'localhost' WITH GRANT OPTION;`
	- 给zhangsan用户赋权限，设置为在test库，temp表，上的id、name、price列只有select 权限
	- `grant update(id,name) on test.shop to zhangsan@'localhost';`
	- 授予用户zhangsan可以对test.shop表的id和name列进行更新

8. 回收权限
	- `revoke PROCESS ON *.* FROM admin@'localhost';`

9. 删除用户
	- `drop user admin@'localhost';`

### 6.其他常用命令
1. `select current_user();` 查看当前登录用户