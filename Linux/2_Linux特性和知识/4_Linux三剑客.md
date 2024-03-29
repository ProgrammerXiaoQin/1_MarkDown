### 一. sed
`sed [-hnV][-e<script>][-f<script文件>][文本文件]`
1. 参数说明
	- `-e` 选项中指定的script来处理文本文件
	- `-f` 选项中指定的script文件处理的文本文件
	- -n 禁止sed编辑器输出，但可以与p命令一起使用完成输出
	- -i  直接在源文件中修改
	- -r , -E  使用扩展正则表达式
	- -s 将多个文件视为独立文件，而不是单个连续的长文件流

2. 动作说明
	- `a` 追加 , 在指定行后追加指定内容
	- c 更改 , 更改匹配行的内容
	- d 删除 , 删除匹配行的内容
	- i 插入 , 在指定的上一行插入一行指定内容
	- p 打印指定行或处理的过的行 , 常 p 会与参数 sed -n 一起运行
	- s 替换 , 替换匹配行的内容
		- g 全局, 替换时没有加这个只会替换找到的第一个
	- q  退出 sed，不再处理更多的文本行
		
3. script语法
	- `"[开始行][结尾行][动作]/[匹配内容]/[替换内容]/[动作]"`   
	- sed默认一行行处理文件 , `[开始行][结尾行]` 设定`sed`要处理的文件或`|`后接收的行, 不写默认处理所有行 , `$` 可以用来表示最后一行
	- `[匹配内容]` 中的文字(可用正则) , 将会被sed匹配 , 可用于打印或替换
	- `[替换内容]` 如果指定了`s`动作 , 那么`[匹配内容]` 会被`[替换内容]`替换
	- `/ / /` 分割符可用其他符号代替 , 如`# , @`等

4. 实例
 - **行新增和删除**
```shell
[canti@localhost ~]$sed -i '2,5d' test.txt
## 删除test.txt中第2~5行的内容
[canti@localhost ~]$sed '2a Drink tea or ......\
drink beer ?' test.txt
## 在第二行增加Drink tea or ......和drink beer ?两行内容, 原本第二三行内容会变到第四五行
[canti@localhost ~]$nl test.txt | sed '3,$d'
## 删除第三行到最后一行的内容
[canti@localhost ~]sed '/^$/d' test.txt
## 删除文件中所有的空行
[canti@localhost ~]$sed -i '3i 10.159.185.40 sf.chtwo.com' /etc/hosts
## 在/etc/hosts 第三行插入10.159.185.40 sf.chtwo.com,本来在第三行的内容会变到下一行
[canti@localhost ~]$ nl test.txt | sed  '/oo/d'
## 删除包含oo的行,其他行输出
```

- **替换与显示**

```shell
[canti@localhost ~]$sed -i '79s#yes#no#g' /etc/ssh/sshd_config
## 把/etc/ssh/sshd_config文件第79行的yes改为no
[canti@localhost ~]&sed '2,5c No 2-5 number' test.txt
## 将test.txt中第2~5行的内容更改为No 2-5 number
[canti@localhost ~]&sed -n '5,7p' test.txt
## 打印5~7行的内容
[canti@localhost ~]&sed -n '/^root/p' test.txt
## 打印root开头的行
[canti@localhost ~]&nl test.txt | sed -n '/oo/p'
## 打印test.txt中包含oo的行
[canti@localhost ~]$sed -i 's/oo/kk/g' ./test*
## 将当前目录下所有test开头的文件中所有的oo替换成kk
[canti@localhost ~]$nl test.txt | sed 's/^.*addr://g' 
## 将任意字符任意个数开头, addr:结尾的字符串替换为空,{^}匹配开头, {.}匹配任意字符,{*}匹配任意次数
[canti@localhost ~]$sed -i 's/\.$/\!/g' test.txt
## 将test.txt中每一行以.结尾的字符替换为!
[canti@localhost ~]$cat passwd -n | sed -n 's/root/chao/gp'
## 把所有root替换为chao, 并打印替换掉行的结果
[canti@localhost ~]$sed -i '/^root/,/^ftp/d' passwd.bak 
## 删除root开头的行到ftp开头的行
[canti@localhost ~]$sed -E 's/(^.)/@\1/' passwd
## 给所有行前面加上@
 ```

-  **数据搜索并执行指令**
```shell
[canti@localhost ~]$nl testfile | sed -n '/oo/{s/oo/kk/;p;q}'
## 查找testfile中包含的oo的行,对这些行执行花括号里的命令,最后的{q}是退出
```
	
- **多点编辑**
	 
```shell
[canti@localhost ~]$nl testfile | sed -e '3,$d' -e 's/HELLO/RUNOOB/'
## 删除 testfile 第三行到末尾的数据，并把 HELLO 替换为 RUNOOB
```


### 2. grep
`grep [options] pattern [files] ## pattern 要查找的字符串或正则`
1. 参数说明
	- -i 忽略大小写
	- -v 反向查找 , 只打印不匹配的行
	- -n 显示行号
	- -r 递归的查找子目录中的文件
	- -l 只打印匹配到内容的文件的文件名 , -L ,-l的取反
	- -c 只打印匹配的数量, 而不是文本行本身
	- -E  使用扩展正则表达式
	- -h 应用于多文件搜索，不输出文件名
	


2. 其他参数说明
	- -A n 指定显示除了匹配行外还显示匹配行下面n行
	- -B  匹配行上面n行
	- -C 匹配行上下n行
	- -w 代表式当做一个完整的单字符来搜寻，忽略哪些部分匹配的行
	- -m 匹配指定次后停止
	- -o  只显示匹配的内容
1. 实例
```shell
$ grep hello file.txt
## 查找file.txt中包含hell的行
$ grep -r -n pattern dir/
## 在dir文件夹中递归查找所有文件中匹配pattern的行
$ echo "hello world" | grep -c world
## 在标注输出中查找包含world的行 , 只打印匹配行数
$ grep test *file
## 查早所有包含file文件名的文件中, 所有包含test的行

```

### 3. awk
AWK 是一种处理文本文件的语言，是一个强大的文本分析工具
`awk [参数] [处理内容] [操作对象]`

1. 参数说明
	- `-F fs` 指定以fs作为分隔符，默认分隔符为空格或者制表符 , 多个连续的空格看做是一个空格的分隔符, 指定多个分隔符要用`[分隔符1分隔符2]+`
	- `-f` 从脚本文件中读取awk脚本指令
	- `-v var=val` 在执行处理过程之前，设置一个变量var，初始值为val

2. 内置变量
	- `NF`    总字段数 , `&NF`则是最后一个字段的值
	- `$0`    代表整个文本行。
	- `$1`    代表文本行中的第一个数据字段。`$2`表示第二个,  `$n`表示第n个
	- `NR`    该值记录行号

3. 内置函数
	- `print`    打印
	- `printf`  格式化打印 ,不会自动换行
		- `%S`  字符串
		- `%d`  数字
		- `%f`  浮点数
		- `-`   左对齐
		- `+`   右对齐 , `+`遇上数字或浮点数时会显示符号
		- `15` 最少占用15个字符
	- `break` 终止循环
	- `continue` 中断本次循环继续下一轮
	- `next` 提前结束对本行的处理而直接进入下一行
	- `rand()` 返回0和1之间一个随机数
	- `length([s])`返回指定字符串的长度

4. 操作符
	- 算术操作符：`x+y，x-y，x*y，x/y，x^y(多少次方)，x%y`
	- 赋值操作符：`=，+=，-=，*=，/=，%=，^=，++，--`
	- 比较操作符：`>，>=，<，<=，!=，==`
	- 逻辑操作符：`&&，||，!`
	

6. 流程控制 
	- `if(){}`   单分支
	- `if(){}else{}`   双分支
	- `if(){}else if(){}else{}`  多分枝
	- `for(){}`  循环, 格式`for(i="初始值";条件判断;游标){}`
	- `while(){}`  循环, 格式`while(条件判断){}`
		

8. 执行过程和生命周期
	- awk运行处理规则执行流程
		1. `BEGIN{}`   仅在开始处理前运行一次 , `BEGIN{}`中可以指定:
			- `OFS=` 在`BEGIN{}`中指定打印分隔符 , 默认为空格
			- `FS=` 指定分隔符 , 默认为空格
		1. `//`           仅处理匹配到的行
		2. `{}`           循环体
		3. `END{}`      仅在处理都完成后执行一次
	- awk的生命周期
		1. awk接收标准输出或文件处理文本,读一行取得一行,知道处理完成
		2. 最开始执行BEGIN{} , 然后将文本一行行交给处理规则处理
		3. 理完成之后的所有数据交给END{}来再次处理
	

3. 实例
```shell
awk '/^$/{print "Blank line"}' test.txt
## 每匹配test.txt中每一个空行就打印一次Blank line
awk -F: '{print NF,$NF,NR}' /etc/passwd
## 以冒号为分隔符,/输出每行总字段数,每行最后一个字和行号
awk  'BEGIN{OFS="<>";FS=":"}{print $NF, $1}' /etc/passwd
## 以冒号为分割符,打印最后一个字段和第一个字段并用"<>"作为分割符
awk -F: 'BEGIN{OFS="|"}{printf "|%+15s|%-15s|\n", $NF, $1}' /etc/passwd
## OFS="|"以|为分隔符, |%+15s| : 以|为分隔符 %s 配合 printf 使用,代替当前## 字符串 右对齐 占用15字符
## |%-15s| : 以 | 为分隔符 %s 配合 printf 使用 代替当前字符串 左对齐 占用
## 15字符 , \n : 换行符
awk -F: '/root/{print $0}' /etc/passwd
## 匹配有root内容的行,输出当前行 
awk -F: '$4 > $3{print $0}' /etc/passwd
## 打印属组ID大于属主ID的行
awk -F: '$NF ~ "bash"{print $0}' passwd
## 打印以:分割 , 最后一个字段中包含bash的行
awk -F '" "|:' '{print $3$5$7}' /data/oldboy/oldboy.txt
## 以" "(空格)或:作为分割符
awk 'NR%2==1{print $0}' passwd
## 打印奇数行
awk -F: '{if($3>$4){print "大于"}else{print "小于"}}' passwd
## 当第三项大于第四项输出大于 , 否则输出小于
awk 'NR==1{for(i=10;i>0;i--){print NR,"---",$i}}' passwd
## 循环打印第一行十次
awk -F: '{i=1; while(i<10){print $0, i++}}' /etc/passwd
## 打印第一行十次
```