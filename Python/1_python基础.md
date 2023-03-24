
### 数据类型

#### 1. Dictionary 字典
	字典是一个无序、可变和有索引的集合。字典用花括号编写，拥有键和值
1. 查找
	- get(key)        参数为一个key名,返回该key对应的value
	- dict\["key"\]    返回key对应的value
	- values()         获取字典所有的value,返回一个dict_values可迭代对象
	- keys()            获取字典所有的key,返回一个dict_keys可迭代对象
	- items()          获取字典的可迭代对象,包含key跟value
2. 修改
	- dict\["key"\] = newValue      修改
	- dict\["key"\] = Value             增加
3. 删除
	- pop("key")       删除具有指定键名的项
	- popitem()     删除最后插入的项
	- clear()           关键字清空字
	- del(dict)        删除字典
	- del\["key"\]    删除键值对

#### 2. Set 集合
	集合是无序,不可变的数据类型.集合用()编写,如果创建一个空集合要加(,)
1. 查找
	- for date in set 判断数据在集合
	- for date not in set 判断数据不在集合
2. 修改
	- add()      增加数据
	- update()  增加序列
3. 删除
	- remove()    删除指定数据,数据不存在就报错
	- discard()     删除指定数据,数据不存在不报错
	- pop()          随机删除数据并返回这个数据

#### 3. String 字符串
1. replace("aa","cc")  , 替换,将字符串中的aa全部替换成cc
2. startswith("aac")  , 判断是否已aac开头,是返回True,否返回False
3. find("aa"),查找字符串是否包含aa，存在返回开始的索引值，否则返回-1
4. index("bb")查找字符串是否包含bb，存在返回开始的索引值，否则返回错误




### 内置方法
1. sorted(iterable, key=None, reverse=False) 
	对序列（列表、元组、字典、集合、还包括字符串）进行排序,返回一个列表对象
	参数:
	`iterable` 可迭代对象,字典(o.items())
	`key`  参数为一个函数,指定列表排序标准
	`reverse` 参数为一个布尔对象,排序方法,默认为False(从小到大)
```python
a = [[2,3],[4,1],(2,8),(2,1),(3,4)] #按元组第一个从小到大排序，如果第一个相同
b = sorted(a,key=lambda x: (x[0], -x[1])) #则按元组第二个从大到小排序
print(b)
```









