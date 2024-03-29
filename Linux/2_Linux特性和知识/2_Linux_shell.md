##### 一.shebang
1. shebang: 脚本文件开头的#!,程序会分析shebang后面的内容,作为解释器的指令
	- **#!/bin/sh** 开头的文件,会用bash做解释器
	- **#!/usr/bin/python** 开头的文件,会用python解释器去执行
	- **#!/usr/bin/env  解释器名称** ,是一种在不同平台上都能找到正确解释器的方法
2. 注意事项 
	- 如果未指定`shebang` ,默认用当前shell去解释脚本,即`$SHELL`
	- 如果`#!`指定的解释程序不存在,指定的解释器则会被忽略,用默认解释器去执行
3. shell脚本执行方式
	- `./hello.sh` 给文件添加执行权,作为命令调用直接执行 
	- `bash hello.sh` ，将脚本文件作为参数传给shell程序来执行
	- `source hello.sh` ,使用`source`命令执行

 
##### 二.shell常量与变量
1. 定义变量时,变量与值之间不得有空格 ,shell自定义变量只作用于当前shell, 变量定义之后，引用变量时需要使用`$`符号, 用source或者点符号调用脚本,会用当前shell加载脚本,因此保留,并可以在sh中修改 , 用bash/sh解释器执行脚本,会开启一个子shell,因此不保留当前shell变量 , 如果想要当前shell和该shell开启的子shell(shell环境)都能识别该变量需要用export导入该变量
```bash
	cat test.sh    ->user1="aaa"
	user1="bbb"
	bash test.sh
	echo $user1    ->bbb
	source test.sh  
	echo $uer1     ->aaa
```
2.  shell变量没有类型 , bash默认把所变量认为是字符串
```shell
	annum=2020
	let "annum+=1"
	echo $annum  >> 2021
	## 使用 let 计算一个算术表达式并且赋值给变量
	b=${annum/202/203}
	echo $b >> 2031
	# 将字符串变量中的 202 替换成为 203
```

3. 单引号变量不识别特殊语法,双引号变量识别特殊语法 ,  ``` `` ``` 返引号,引用命令执行结果,效果与`$()`和`()`相同
```shell
	name='${奥利给}'
	name2="奥利给"
	name3="${name2}"
	echo name  -> ${奥利给}
	echo name3   ->  奥利给
```

4. shell变量默认是全局变量, 对整个shell文件有效. 声明局部变量需要用`local`关键字 , 其只在声明所在的块或者函数当中可见

5. shell 支持多种进制的整型常量 , `0`开头的八进制 , `0x`开头的十六进制 . 对于特殊进制可以用 `进制#数字`表示 如: `$((3#120))` 表示三进制的数字`120`, 输出十进制为`15`

##### 三.系统变量,特殊变量
1. 环境变量:
	- 用于定义shell运行环境,保证shell命令正确执行,环境变量可以在命令行中临时创建,但用户退出shell端变量即丢失,要永久生效则需修改配置文件
		- 用户个人配置文件`~/.bash_profile`,远程登陆用户特有文件`~/bashrc`
		- 全局配置文件`/etc/profile` , `/etc/bashrc` ,建议在`/etc/profile.d`,并非直接修改主文件,修改全局配置文件影响所有用户
		- 个人用户配置文件优先全局配置文件
		
	- 环境变量的定义通常都是约定俗成的，例如：`PATH`定义了 shell 进程查找命令程序的路径。
	
	- shell 当中的任何变量都可以通过`export`导出为环境变量，环境变量可以被子进程继承，因此也可以被视为父子进程信息传递的一种方式
	
```shell
	echo $PATH
	/usr/local/bin:/usr/bin:/usr/local/sbin
	export PATH="$PATH:/workspace"
	echo $PATH
	/usr/local/bin:/usr/bin:/usr/local/sbin:/workspace
```

2. 与变量的命令和语法
	- **set**                 输出所有变量，包括环境变量，局部变量
	- **env**                只显示全局变量
	- **declare**          输出所有变量，如set
	- **export**           显示和设置环境变量
	- **unset 变量名** 删除变量或函数
	- **readonly**        设置只读变量,函数

3. 特殊变量
	- 参数变量 ,是指调用 shell 脚本时，按照命令行位置进行引用的参数。脚本当中按照`$0`、`$1`、`$2`的顺序逐个进行引用，依此类推。其中`$0`就代表脚本名,其他相关参数有
		- `$#` 获取shel脚本后面的参数个数
		- `$*` 代表全部命令行参数，全部参数作为一个字符串
		- `$@`  代表所有命令行参数，每个参数都是一个独立的字符串
		
```shell
#!/bin/bash 
# parameter.sh  
echo $0,$1,$2,$3
echo $#
touch "$*"
# 由于将全部参数作为 1 个字符串，所以使用全部参数为名称创建 1 个文件  
touch "$@"
# 因为参数为 3 个字符串，所以分别使用每个参数作为名称，共创建 3 个文件

>>> bash parameter.sh 2019 2020 2021
>>> parameter.sh 2019 2020 2021
>>> 3
```

4. 状态变量
	- **$?**  值为0时表示上一条命令执行成功,1-255为错误码 ,只能使用一次，使用完以后就会被目前命令的返回值所替换
	- **\$\$** 获取当前进程号
	- **$!** 上一次后台进程PID
	- `$_` 取得上一次命令的最后一个参数

3. 常见系统内置变量
	- **$BASH_SUBSHELL**  变量值为0时表示在当前shll执行 


##### 四. 运算
1. 数学运算符包括`+`、`-`、`*`、`/`、`%`以及幂运算`**`，Bash Shell 本身只支持整数运算，如果需要使用到浮点运算，则可以调用`bc`和`dc`等外部命令
```shell
 echo $((3.14+2))
 -bash: 3.14+2：语法错误: 无效的算术运算符 (错误符号是 ".14+2")
```
2. 逻辑操作符 : `&&`和`||`，如果`&&`左侧表达式为`false`，则右侧表达式无需执行即可确定整个表达式的结果为`false`；如果`||`左侧表达式为`true`，则右侧表达式无需执行即可确定整个表达式的结果为`true`；
```shell
>>> true && date
>>> 2020年 07月 13日 星期一 04:58:28 CST
>>> false && date
>>>
```

3. 运算符命令
	- let 用于整数运算,类似的有(()),expr,$[]
	- awk 可以用于整数或浮点数运算
	- declare 定义变量值和属性
```shell
	((i=i+1)) #i+1的值赋值给i
	i=$((i+1))
	echo $((6>7&&3<4)) 
```

 4. **a++,++a**的区别
	a++,先对变量a操作,再+1
	++a,先计算+1,在赋值给a
```shell
	a=5
	echo $((++a)) ->6
	echo $((a++)) ->6
```

##### 五.内置命令
注:外置命令一定会开启子进程执行,内置命令不会,内置命令和shell是一体的,系统启动后就存在内存中,compgen -b可以查看所有内置命令
1. echo               打印
	- -n   不换行输出
	- -e    解析字符串中的特殊符号,类似于printf
	- \\n   换行
	- \\r    回车
	- \\t   制表符  (tab 四个空格)
	- \\b 退格

2. eval        当一个命令前面放上eval时，其结果是shell在执行命令行之前扫描它两次。如：
```shell
pipe="|"
eval ls $pipe wc -l
```
shell第1次扫描命令行时，它替换出pipe的值｜，接着eval使它再次扫描命令行，这时shell把｜作为管道符号了

3. exec       执行命令后自动exit

##### 六.函数
1. shell中有两种定义函数的方法, 一种是用`function`关键字定义:
```shell
function function_name(){
	# command
}
```

2. 另一种与c语言中的类似,这种方式可移植性更好，更加推荐使用:
```shell
function_name(){
	#command
}
```

##### 七.条件测试
1. shell 提供了一系列条件测试运算符，用于判断某种条件是否成立，条件测试运算符主要包含如下 3 种
```shell
	test expression  
	[ expresssion ]    # 条件和左右括号之间必须带有空格  
	[[ expression ]]   # 新版本 Bash Shell 提供
```

2. 文件测试 , 文件测试通常用于判断文件属性，常用的文件测试条件如下:
	- `-e`    文件存在      `[ -e ~/.bashrc ]`
	- -f     普通文件      `[ -f ~/.profile ]`
	- `-s`    文件长度不为0   `[ -s /etc/mtab ]`
	- `-d `   文件是目录  `[ -d /etc ]`
	- `-r,-w,-x` 文件可读,可写,可执行 
	- `file1 -nt file2`  文件1比文件2新   `[ ~/.bashrc –nt ~/.profile ]`
	- `file1 -ot file2` 文件1比文件2跟旧 `[ ~/.bashrc –ot ~/.profile ]`
	- `file1 -ef file2`  两个文件是同一个硬链接
	- `!`     取反测试结果  `[ ! -d ~/.profile ]`

3. 整数比较
	- `-eq`     等于       `[ "$m" -eq "$n" ]`
	- `-ne`     不等于
	- `-gt`     大于
	- `-ge`     大于等于
	- `-lt`     小于
	- `-le`     小于等于
	- `< , <= , > , >=`  需要以`(())`方式测试 , `(( "$m" < "$n"))`
    

4. 字符串比较
	- `=`      相等           `[ "$str1" = "$str2" ]`
	- `!=`    不相等       `[ "$str1" != "$str2" ]`
	- `-z`    长度为0     `[ -z "$str" ]`
	- -n   长度不为0 ,在`[]`当中使用时，需要将字符串放入`""`里面  `[ -n "$str" ]`

5. 混合比较 , 条件测试支持在多个表达式之间进行逻辑运算 , `-a`表示**与运算**，`-o`表示或运算
```shell
#!/bin/bash  
# compare.sh  
[ "$1" -ge 0 -a "$1" -le 100 ] && echo yes || echo no  
## 第一个参数是否大于等于0,并且小于100 ,是则输出yes,否则输出no
[ "$1" -lt 0 -o "$1" -gt 100 ] && echo no || echo yes
## 第一个参数是否小于0,或者大于等于100 ,是则输出no,否则输出yes
```

##### 八.逻辑控制
1. **if then** 单分支
```shell
if 条件  
  then  
    代码块  
fi
```

2. 双分支
```shell
if 条件; then  
    代码块 1  
  else  
    代码块 2  
fi
```

3. 多分支
```shell
if 条件1; then  
    # 代码块 1  
  elif 条件2; then  
    # 代码块 2  
  # ... ...  
  elif 条件n; then  
    # 代码块 n  
  else  
    # 代码块 n+1  
fi
```

4. **for**循环 ,for 循环的`列表`是一个由空格分隔的字符串列表，支持通配符。如果缺省，则会自动使用当前的命令行参数列表`$@`
```shell
for 参数 in [列表]  
  do  
    命令  
done
```

```shell
#!/bin/bash  
# week.sh  
if [ "$1" == "工作日" ]; then  
  for wd in Monday Tuesday Wednesday Thursday Friday; do  
    echo $wd  
  done  
elif [ "$1" == "非工作日" ]; then  
  for wd in Saturday Sunday  
    do  
      echo $wd  
    done  
else  
  echo "输入错误！"  
fi
## 根据输入的参数，分别循环打印工作日和非工作日
```

```shell
#!/bin/bash  
# file.sh  
for filename in *.c  
  do  
    echo $filename  
  done
## `*.c`会被展开为当前目录下所有`.c`后缀的非隐藏文件
```

```shell
#!/bin/bash  
# loop.sh 
for ((i=0; i < 7; i++))  
  do  
    echo $i  
  done
## 循环指定次数
```

5. while循环
```shell
# Shell 风格  
while [条件]  
  do  
    命令  
  done  
  
# C 风格  
while ((表达式))  
  do  
    命令  
  done
```

6. 跳出循环
	- break 跳出整个循环
		- 用法 :`break n`
		- n表示跳出循环层数, 缺省默认为当前循环
	- continue 跳出本次循环

##### 九.语法
1. **${变量}** 返回变量值
2. **${#变量}** 返回变量字符长度
3. **${变量:start}** 截取变量下标start后的字符,包含start
4. **${变量:start:length}** start提取的字符,受length限制
5. **${变量#word}** 从变量开头删除匹配的word,开头不是word不删,支持通配符
6. **${变量##word}** 从变量开头删除匹配的word(贪婪模式)
7. **${变量%word}**  从结尾向前匹配
8. **${变量%\%word}**  从结尾向前匹配(贪婪模式)
```shell
	name="yuchao"
	name2="yuyuyuchao"
	echo ${#name}    ->6
	echo ${name:4}   -> ao
	echo ${name:2:3}   ->cha
	echo ${name#yu}    ->chao
	echo ${name#ch}    ->yuchao
	echo ${name2#*c}  ->hao
	echo ${name2%c*}   ->yuyuyu
```

8. **${变量/pattern/string}** 用string替换找到的第一个pattern
9. **${变量//pattern/string}** 用string替换所有找到的pattern
10. **${parameter:-word}** 如果parmeter为空,返回word
11. **${parameter:=word}** 如果parmeter为空,parmeter== word,并返回word
12. **${parameter:?word}** 如果parmeter为空,word当做stuerr输出
13. **${parameter:+word}** 如果parmeter不为空,返回word 否则什么都不做

14. **()** 用()括起来的语句会明确开启一个子shell运行命令
	`shell脚本开发中,经常用子shell进行多进程处理,提升并发效率.`
```shell
	(pwd;(ls -l;(echo $BASH_SUBSHELL)))   #其中BASH_SUBSHELL的值为3
```

 

##### 十.通配符
1. `*` 匹配任意多个字符(包括零个或一个)
2. `?` 匹配任意一个字符(不包括零个)
3. ^ 匹配行首
4. $ 匹配行尾
5. **.** 匹配任意字符
6. `[!characters]` 匹配任意一个不是字符集中的字符
```shell
	ls   ->  datea.txt..daten.txt
	ls date[!abc] ->dated.txt..daten.txt
```
4. `[:digit:]` 匹配任意一个数字
5. `[:alpha:]` 匹配任意一个字母
6. `[:lower:]` 匹配任意一个小写字母
7. `[:upper:]` 匹配任意一个大写字母
```shell
	Date??? #匹配任意Date开头,后面跟着三个字符的文件
	[abc]*  #匹配任意a或b或c开头的文件
	backup.[0-9][0-9][0-9] #匹配任意已backup.开头并紧跟三个数字的文件
	[![:digit:]]*  #匹配任意不是数字开头的文件
	*[[:lower:]123]  #匹配任意一个已小写字母或数字1&2&3结尾的文件
```
##### 十一. bash脚本注意事项
1. 当我们脚本里有`cp , mv ,rm `命令时 , 后面的参数有时候会带变量 , 如`rm -rf $TMP_DIR/data` , 有时候会因为修改脚本等原因导致变量没有赋值 , 导致执行结果出错 , 如上面里例子就会变成`rm -rf /data` 导致出现问题
	- 解决办法 : 在脚本开头加上 `set -euo pipefail` 
	- `-u` Bash会把所有未定义的变量视为错误
	- `-e` 脚本在出现异常时马上退出
	- `set -o pipefail` Bash只会检查管道（pipeline）操作最后一个命令的返回值 , 加上这个选项后只要有任何一个命令失败（返回值非0），则整个管道操作被视为失败

