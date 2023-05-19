#python

# 1. 数据类型
Python 是弱类型语言，使用前不需要专门声明，赋值之后变量即被创建。Python 一共拥有 5 种标准的数据类型：**数值**（_Number_）、**字符串**（_String_）、**列表**（_List_）、**元组**（_Tuple_）、**字典**（_Dictionary_）。Python 这 5 种标准数据类型除了通过字面量方式声明之外，还可以通过构造函数`int()`、`float()`、`complex()`、`str()`、`list()`、`tuple()`、`dict()`进行声明。

##### 1.列表List
>列表是有序可变的数据类型, 列表的元素可以重复如果需要比较列表可以用 operator 模块的 eq 方法, 内置函数len(list)可以获取list的长度

1. 访问列表中的值
	- `list[0]` 通过索引访问, 索引可以从尾部开始,倒数第一为`-1` , 往前一位为`-2`，以此类推
	- `nums[0:4]` 切片, 通过下标访问列表中从0开始,到下标4的前一位,前开后闭

2. 更新列表
	- `list[2] = 2001` 修改指定索引的值
	- `append()`在列表末尾添加新的对象 , 也可以用 `+=` 往列表末尾添加新元素
	- `insert(n, obj)` 在列表指定位置添加元素

3. 删除元素
	- `del list[2]` 删除指定索引元素
	- `remove(obj)` 删除指定元素
	- `pop(n)` 删除指定索引元素,没有参数默认删除最后一个

4. 列表函数和方法
	`python内置函数list(seq)可以将元组或字符串转换为列表`
		
	- `append()`在列表末尾添加新的对象
	- `count(obj)`  统计元素在列表中出现次数
	- `extend(seq)` 在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）
	- `index(obj)` 返回列表中第一个匹配项的索引
	- `insert(index, obj)` 将对象插入列表
	- `pop(defalut -1)` 移除列表元素(默认为最后一个), 并返回该元素
	- `remove(obj)` 移除列表中某个值的第一个匹配项, 如果没有则会报错
	- `reverse()` 列表倒转
	- `sort(key=None, reverse=False)` 对原列表排序, 参数参考[[#1.sorted(iterable, key=None, reverse=False)|sorted()]]
	- `clear()` 清空列表
	- `copy()` 复制列表
 
##### 2.元组Tuple
>元组与列表相似, 但是元组元素不能修改,元组只包含一个元素时，需要在元素后面添加逗号 , 否则括号会被当作运算符使用 , 如tup1 = (50, )

1. 访问元组 :  与List相似

2. 删除元组:  只能用del语句删除整个元组,

3. 内置函数
	`python内置函数tuple(iterable)可以将可迭代系列转换为元组`
	- `len(tuple)`
	- `max(tuple)`
	- `min(tuple)`

##### 3. Dictionary 字典
>字典是一个无序、可变和有索引的集合。字典用花括号编写，拥有键和值, 具有极快的查找速度, dict的key必须是**不可变对象**
1. 查找
	- `get(key)`        参数为一个key名,返回该key对应的value
	- `dict["key"]`    返回key对应的value
	- `values()`         获取字典所有的value,返回一个dict_values可迭代对象
	- `keys()`            获取字典所有的key,返回一个dict_keys可迭代对象
	- `items()`          获取字典的可迭代对象,包含key跟value
2. 修改
	- `get(key)` = newValue      修改
	- `dict["key"]` = Value             增加
3. 删除
	- pop("key")       删除具有指定键名的项
	- popitem()     删除最后插入的项
	- clear()           关键字清空字
	- del(dict)        删除字典
	- del\["key"\]    删除键值对

4. 字典内置函数&方法
	- `len(dict)` 计算字典元素个数,内置函数
	- `clear()` 删除字典所有元素
	- `dict.fromkeys(seq[, value])` 创建新字典,以序列seq中元素为键,可选value为初始值
	- `get(key, default=None)` 返回指定键的值,如果不在字典中返回default设置的默认值
	- `setdefault(key, default=None)` 与get()类似,如果key不在字典中将会设置键并将值设为default
	- `dict.update(dict2)` 把字典2的键值对更新到dict里
	- `pop(key[, default])` 移除key对应的键值对,并返回该value

##### 4. Set 集合
>集合(set)是无序,不可重复的数据类型序列.可以使用大括号 { } 或者 set() 函数创建集合,注:创建空集合必须用`set()` 而不是`{}` , `{}` 是用来创建空字典的, `set` 对象可以理解为只包含key的dict (key不可重复,无序), 同理set不可放入可变对象如:list
1. 查找
	- for date in set 判断数据在集合
	- for date not in set 判断数据不在集合
2. 修改
	- add()      增加数据
	- update()  增加元素,参数可以是一个序列
3. 删除
	- remove()    删除指定数据,数据不存在就报错
	- discard()     删除指定数据,数据不存在不报错
	- pop()          随机删除数据并返回这个数据
4. set内置方法和函数
	- `add()` 添加元素
	- `clear()` 移除所有元素
	- `copy()` 拷贝集合
	

##### 5. String 字符串
1. 转义字符
	- `\(在行尾时)` 续行符
	- `\a`  响铃
	- `\b`  退格
	- `\n` 换行
	- `\v` 纵向制表符
	- `\t` 横向制表符

2. 字符串运算符
	- `+` 字符串链接
	- `*` 重复输出指定次数字符串
	- `r` 字符串前面加r里面所有的字符串都是直接按照字面的意思来使用

3. format格式化字符串
	-  数字格式化
		- `{:.2f}` 保留小数点后两位
		- `{:+.2f}` 带符号保留小数点后两位
		```python
		>>> print("{:.2f}".format(3.1415926))
		>>> 3.14
		>>> '{:b}'.format(11) >>>1011
			'{:x}'.format(11) >>>b
			'{:#x}'.format(11) >>>0xb
			'{:#X}'.format(11) >>>0XB
		```
		
4. 内置函数
	- `endswith(suffix,beg=0,end=len(string))` 判断字符是是否以suffix结束,beg和end可以指定检查范围,如果是返回True,否则返回False
	- `startswith("aac")` , 判断是否已aac开头,是返回True,否返回False 
	- `index("bb")` 查找字符串是否包含bb，存在返回开始的索引值
	- `find("aa")`,查找字符串是否包含aa，存在返回开始的索引值
	- `len()`  返回字符串长度
	- `lower()` 字符串中所有大写转换为小写
	- `lstrip()` 截掉字符串两边的空格或指定字符(str参数)
	- `replace("aa","cc")`  , 替换,将字符串中的aa全部替换成cc
	- `rfind()` ,  类似于find,从右边开始查找
	- `upper()` 字符串中所有小写转换为大写






##### 6.Float浮点数
1. 浮点数运算问题
`由于python中浮点数是以二进制形式存储的,浮点数转换为二进制过程中出现无限循环情况, 照成运算结果不精确(python是以双精度(64bit) 来保存浮点数, 后面多余的会砍掉)`
- 解决方案:`from decimal import Decimal`
	```python
	from decimal import Decimal
		a = Decimal('1.414062')
		b = Decimal('0.000001')
		print(a+b) ->1.414062
	```






# 2.常用内置函数和方法
#### 1.sorted(iterable, key=None, reverse=False) 
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

#### 2.eval()



# 3.python运算符
1. 逻辑运算符
	- **and:**  `x and y` 如果 x 为 False，返回 x 的值，否则返回 y 的计算值。
	- **or:**   `x or y`    如果 x 是 True，它返回 x 的值，否则它返回 y 的计算值。
	- **not:** `not x`      如果 x 为 True，返回 False 。如果 x 为 False，它返回 True。

2. 身份运算符
	- **is:** 判断两个标识符是不是引用自一个对象, `x is y` 类似于`id(x) == id(y)`
	- **not is:** 判断两个标识符是不是引用自不同对象

 






# 4.作用域
1. Python 当中仅`module`模块、`class`类、`def`或`lambda`函数会引入新作用域，`if/elif/else`、`try/except`、`for/while`等代码块并不会引入新作用域，即这些语句当中定义的变量在其外部也能访问。
2. 