### 1. 修改配置
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