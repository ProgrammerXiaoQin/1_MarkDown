### 一. 常用命令
 1. **touch**  创建文件
	- touch 1.txt 创建个名为1.txt的文本文件
	- touch {1..100}.txt 创建1.txt~100.txt,共一百个文件

 2. **cd** /etc/sysconfig/network-scripts/进入目录 更改ipv4地址
	vi ./ifcfg-(网卡名) 例:ifcfg-ens33,ifcfg-lo

 3. **systemctl restart network**  重启网络服务
	 - status  查看服务运行状态
	 - enable 开机后自动开启
	 - disable 关闭,重启后也不会开启
	 - start     临时开启
	 - stop     临时关闭

 5. **history**   查看当前执行的历史命令
	- -w保存命令到历史文件中
	- -c 清空命令历史记录
	- -d 删除历史命令第n行
	- -r  恢复历史命令(从/root/.bash_history中)
	- !77 指定条目 执行历史命令中的第77条命令
	
 6. **alias**  设定及查看别名	alias net='vim /etc/sysconfig/network-scripts/ifcfg-ens33' (注意不要多打空格)
	 - 直接使用`alias` 查看当前bash环境所有别名
	  - unalias 取消别名	unalias = net
		  注:这种方式是在终端命令行下直接执行alias命令创建别名，当会话结束或中断的时候，则创建的别名自动失效
		  如果想要永久有效需要将alias创建的别名添加到/etc/bashrc配置文件中
	  
 7. **date**  设定及查看时间
	 - `date` 查看时间
	 - -s 设定时间
	 - 例:date  -s   11:11:11
	 - 格式格式化输出 `date "+opthion"`
		 - `%Y` 年 ,如`2021`
		 - `%m` 月 , 如`01-12`
		 - `%d` 日 ,如`01-31`
		 - `%H` 小时,二十四小时制
		 - `%I` 十二小时制
		 - `%M` 分
		 - `%S` 秒
	 ```
	 date   -s   2019-12-11
	 date   -s  "2019-11-12   17:23:13"
	 $ date "+%Y-%m-%d,%I:%M:%S"  >> 2023-11-20,10:03:45
	 ```

8. **shutdown** 
	- -c 取消已执行的shutdown命令
	- -h 关机
	- -r 重启
	- shutdown -r now 现在重启
	- shutdown -r 05:30 指定时间重启
	- shutdown -r 05:30& 把定义重启命令放在后台
	- shutdown -r +10 十分钟重启
	- 类似命令**reboot** 重启,**halt,poweroff** 关机,**init 0** 关机(调用系统0级别),**init 6** 重启(调用系统的6级别)

9. **mkdir** 创建文件夹 
	- -p   连级创建
	- mkdir /date{01..50}
	- 在date目录下创建date01,date02..date50

10. **rm** 删除文件
	- -f  "force"(强制)不用确认,直接删除
	- -r "recursive"(递归)删除文件夹(如果文件夹里面有其他文件会一块删除)

11. **cp** 复制文件 
	- cp item..  directory
	- -a  复制文件和目录以及他们的属性,包括所有权和权限.
	- -i  "interactive"(交互式) 重写之前已存在的文件之前提示用户确认,没这个选项则默认重写
	- -r  递归复制目录以及目录中存在的内容(-a)也有同样效果
	- -u 复制时,仅复制目标中不存在的文件,或者文件内容新与目标文件的文件

12. **mv** 移动或重命名file,dir
	- mv filename newfilename
	- mv dirname newdirname
	- -i,-u,-r同cp

13. **top** 查看系统进程的动态运行情况
	- 按1 在整体和各个cpu之间切换
	- 按P 要求top按cpu占用率排序
	- 按M 要求top按内存(RES)占用排序
	- `top [-d number] | top [-bnp]`
	- -d 表示top命令显示的页面更新一次的间隔
	- -p 指定特定的pid进程号
	
```
top - 17:17:30 up 20 min,  1 user,  load average: 0.00, 0.01, 0.04
Tasks: 100 total,   1 running,  99 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  2895008 total,  2342540 free,   223832 used,   328636 buff/cache
KiB Swap:  4194300 total,  4194300 free,        0 used.  2514780 avail Mem

load average: 系统平均负载百分比，分别代表过去1分钟、5分钟和15分钟内的平均系统负载

Tasks: 进程状态，total总进程，running正在运行的任务数，sleeping睡眠状态任务数，stopped已停止的任务数，zombie僵尸进程(已完成但父进程尚未对其进行清理的进程)数量

%Cpu(s):CPU使用率
	us(user):用户空间的应用程序消耗的CPU时间百分比
	sy(system):系统内核耗的CPU时间百分比
	ni(nice):通过nice值调整过优先级的进程所消耗的CPU时间百分比
	id(idle):CPU处于空闲状态的时间百分比
	wa(iowait):CPU 等待I/O操作完成的时间百分比
	hi(irq):处理硬件中断所消耗的 CPU 时间百分比
	si(softirq):处理软件中断所消耗的 CPU 时间百分比
	st(steal):在虚拟化环境中，虚拟机等待获取 CPU 时间的时间百分比
```

14. **ls** 查看目录信息 
	- -a 列出所有文件,包括隐藏文件
	- -l    显示文件详细信息,可简写为ll
	- -ld  显示目录详细信息
	- -lt  按时间排序显示数据信息
	- -lS  按大小排序显示数据信息
	- -lh   已可读凡是显示文件大小信息
	- -li   显示文件详细信息和索引节点号
	- -r   以相反顺序显示文件结果

15. **wc**  统计文件的字节数、字数、行数
	- -l   统计行数(lines)
	- -L 打印最长行的长度

16. **free** 显示空闲内存数量
```
[root@47 temp]# free -h
              total        used        free      shared  buff/cache  available
Mem:           1.7G        315M         98M        724K        1.3G        1.2G
Swap:            0B          0B          0B

total:总内存
used：已使用内存
free：真正用剩余内存
shared：: 表示多个进程共享的内存总额（通常为0，已被废弃）
buff：写入磁盘内存缓冲区的大小,用于提高磁盘写入效率
cache:磁盘中读取内容的缓存大小,用于提高磁盘读取效率
available：操作系统可以立即分配给进程的内存，包括buff/chache中部分可被释放的缓存和free
```

18. **ln** 创建硬链接 `ln [源文件] [链接文件]`
	- `ln file link`   创建硬链接
	- `ln  -s  item link`  创建符号链接 item可以是文件或者目录
	- 硬链接不能关联它所在文件系统之外的文件(也就是链接不能关联与链接所在磁盘不在一个文件磁盘分区上的文件)
	- 硬链接本身与文件没有区别,类似于给文件起一个别名,当一个硬链接被删除时,这个链接被删除,但是文件本身的内容依然存在
	- 硬链接本质是时同一个文件,创建新的硬链接，链接数会增加，删除硬链接，链接数减少,删除源文件只是链接数-1,链接文件访问不影响
	- 符号链接(软连接)生效,是通过创建一个特殊类型的文件,这个文件包含一个关联文件或目录的指针.在一个符号链接中写入内容,那么它关联的文件也被写入,当删除一个符号链接时,只有这个链接被删除,而不是文件自身.如果是文件被删除,那么这个链接依然存在只是不指向任何内容,创建软连接可以用相对路径或者绝对路径
	 `硬链接`
	-  与普通文件没什么不同，inode 都指向同一个文件在硬盘中的区块
	-   以文件副本的形式存在，但不占用实际空间
	-   硬链接只有在同一个文件系统中才能创建
	`软连接`
	-   保存了其代表的文件的绝对路径，是另外一种文件，在硬盘上有独立的区块
	-   访问时替换自身路径
	-   软链接可以对目录进行链接
	-   软链接可以对一个不存在的文件名进行链接
	-   软链接可以跨文件系统

18. **cat** 查看文本文件
	- -n 显示文件行数
	- -E 每行结束处显示"$"
	  

### 二.管道和重定向
1. **>** stdout重定向,标准输出重定向
	- ps > out.txt
	- 执行ps命令,把ps命令内容重定向到out.txt
	- 如果该文件不存在则创建文件
1. **2>** stderr重定向,标准错误重定向
2. **&>** stdout,stderr重定向
3. **<** stdin重定向,标准输入重定向
4. **|** 管道符 ,用于连接 Linux 命令，前一条命令的标准输出会成为下一条命令的标准输入。管道的最大特点在于是管道符`|`两边分别属于不同的进程。
5. **>>** 追加重定向
	- 在原文件尾部追加新的内容


### 三.bash特性
1. Tab键 , 自动补全参数
2. Ctrl-r , 搜索命令行历史记录 , 重复按下Ctrl-r向后搜索 , 回车键执行 , 右方向键将命令放到当前行 , 不直接执行
3. Ctrl-w , 删除最后一个单词 , Ctrl-u , 删除光标前内容 , Ctrl-k , 删除贯标后的内容
4. Ctrl-b和f , 按单词移动贯标 , Ctrl-a可以将光标移动到行首 , Ctrl-e移动到行尾
5. bash的 `here documents` 定义长文件输入符号
	- 当shell脚本或命令需接收多行文件作为输入时候 ,` here documents`允许在脚本中嵌入多行文本
	- 长用的分割字符为`EOF`
```bash
command << delimiter 
	your text here 
	more text 
delimiter
#在这里delimiter是一个自定义的分隔符,你可以选择一个不会与文本内容冲突的字符串
```
6. 进程替换操作符 `<(...)`
	- 将括号中的命令的输出作为一个文件名传递给外部命令
	- e.g.  `source <(kubectl completion zsh)`
