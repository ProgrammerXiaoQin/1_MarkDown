### Linux基础知识
1. Linux中一切皆文件

2. 目录结构
	- /bin     ->/usr/bin      存放系统命令文件(binary)
	- /sbin    ->/usr/sbin     存放只有root用户可以执行的命令
	- /boot                          存放系统引导信息
	- /dev                            存放系统设备文件信息
	- /etc                             存放系统或服务配置文件信息
	- /home                         存放家目录信息
	- /root                            管理员家目录
	- /var                              存储可变文件信息(日志文件)(variable)
	- /mnt                            临时挂载点
	- /opt                             存储第三方软件程序
	- /usr                             存储第三方软件程序
	- /proc                           存放系硬件信息/系统内核配置信息
	- /tmp                            数据临时存储
	- /lib                               系统或软件服务程序库文件存储目录
	- lib64                            系统或软件服务程序库文件存储目录
	
3. 权限  查看文件详细信息 :-  rwx  rw-  ---
	- 第一个- 表示为这时一个普通文件,d表示文件夹,l表示这是一个连接
	- 中间之后的rwx表示当前用户对这个文件的权限r:读取,w:写入,x:可执行
	- 用数字指代r:4,w:2,x:1
	- chmod可以用数字设置权限
	- `chmod 766 file`
	- 就表示设置文件所有者rwx , 同组用户和其他用户rw

4. 命令框用户标识[root@oldboy~]#
	- root           当前登陆系统用户名(超级管理员)
	- oldboy       主机名称
	- ~                当前用户家目录
	- \#                当前用户为超级管理员  , $ 表示普通用户

5. 匹配
	- ^          行首匹配
	- $           行尾匹配

6. 快捷键
	- crtl+d  在标准输入上产生一个文件结尾,可以直接退出Shell命令
	- ctrl+a   移动光标到行首
	- ctrl+e   移动光标到行尾
	- ctrl+k   剪切光标之后的内容
	- ctrl+y   粘贴
	- ctrl+u   删除光标之前的内容
	- ctrl+w  删除光标之前的字符串(按空格分割)
	- ctrl+z   暂停当前程序运行
	   `jobs 查看暂停的程序,fg  +序号重新运行暂停的程序`
	- ctrl+s   进入远程链接锁屏状态
	- ctrl+q   退出远程链接锁屏状态

7. **sync** 将内存中尚未更新的数据写入硬盘
	`建议关机,重启等操作前多用几次`

8. 系统编码设置 LANG="en_US.UTF-8"
	- 临时设置,修改$LANG参数
	- 永久修改,修改`/etc/locale.conf` 配置文件
	- **localectl** 查看当前系统编码

9. 系统远程链接优化
	- 修改配置文件/etc/ssh/sshd_config
	- 79 GSSAPIAuthentication yes   yes改为no
	- **sed  -i  '79s#yes#no#g'    /etc/ssh/sshd_config** 用sed命令修改文件
	- 115 \#UseDNS yes              去掉#,yes改为no
	- **sed -i '115s@#UseDNS yes@UseDNS no@g' /etc/ssh/sshd_config** 用sed命令修改 
	- bash-completion 补全服务名称,通过yum下载

10. 系统级别  `runlevel  #查看系统当前级别`
	- 0 关机 init0
	- 1 单用户模式,不会运行网络服务
	- 2 多用户级别,不支持网络服务
	- 3 系统多用户级别,常用的
	- 4 系统预留,未设置
	- 5 系统进入图形界面
	- 6  重启 init6
	- `ll /usr/lib/systemd/system/runleve*target` 查看系统可以设置级别文件
	- **systemctl get-default** 获取当前系统级别
	- **systemctl set-default 设置级别文件** 设置系统永久级别
	- `graphical.target` 图形模式 , `multi-user.target` 字符模式

11. 什么是命令
	- 一个可执行程序,就像`/usr/bin`中的文件一样,可以是用C或C++写成的程序,可编译成二进制文件, 或脚本语言写成的程序例如`shell`, `python`等
	- 一个内建于shell自身的命令,如`cd`
	- 可以是个shell函数
	- 可以是个命令别名
	`附:`
	- `type command` 显示命令类型
	- `which command` 显示可执行程序的位置,如`which ls`

### 一. 常用命令
 1. touch 创建文件
	- touch 1.txt 创建个名为1.txt的文本文件
	- touch {1..100}.txt 创建1.txt~100.txt,共一百个文件

 2. cd /etc/sysconfig/network-scripts/进入目录 更改ipv4地址
	vi ./ifcfg-(网卡名) 例:ifcfg-ens33,ifcfg-lo

 3. systemctl restart network 重启网络服务
	 - status  查看服务运行状态
	 - enable 开机后自动开启
	 - disable 关闭,重启后也不会开启
	 - start     临时开启
	 - stop     临时关闭

 5. history	查看当前执行的历史命令
	- -w保存命令到历史文件中
	- -c 清空命令历史记录
	- -d 删除历史命令第n行
	- -r  恢复历史命令(从/root/.bash_history中)
	- !77 指定条目 执行历史命令中的第77条命令
	
 5. alias 设定别名	alias net='vim /etc/sysconfig/network-scripts/ifcfg-ens33' (注意不要多打空格)
	  - unalias 取消别名	unalias = net
		  注:这种方式是在终端命令行下直接执行alias命令创建别名，当会话结束或中断的时候，则创建的别名自动失效
		  如果想要永久有效需要将alias创建的别名添加到/etc/bashrc配置文件中
	  
 6. date -s                             设定时间
	 - 例:date  -s   11:11:11
	 ```
	 date   -s   2019-12-11
	 date   -s  "2019-11-12   17:23:13"
	 ```

7. **shutdown** 
	- -c 取消已执行的shutdown命令
	- -h 关机
	- -r 重启
	- shutdown -r now 现在重启
	- shutdown -r 05:30 指定时间重启
	- shutdown -r 05:30& 把定义重启命令放在后台
	- shutdown -r +10 十分钟重启
	- 类似命令**reboot** 重启,**halt,poweroff** 关机,**init 0** 关机(调用系统0级别),**init 6** 重启(调用系统的6级别)

8. **mkdir** 创建文件夹 
	- -p   连级创建
	- mkdir /date{01..50}
	- 在date目录下创建date01,date02..date50

9. **rm** 删除文件
	- -f 不用确认,直接删除
	- -r 删除文件夹(如果文件夹里面有其他文件会一块删除)

10. **cp** 复制文件 
	- cp item..  directory
	- -a  复制文件和目录以及他们的属性,包括所有权和权限.
	- -i  重写之前已存在的文件之前提示用户确认,没这个选项则默认重写
	- -r  递归复制目录以及目录中存在的内容(-a)也有同样效果
	- -u 复制时,仅复制目标中不存在的文件,或者文件内容新与目标文件的文件

11. **mv** 移动或重命名file,dir
	- mv filename newfilename
	- mv dirname newdirname
	- -i,-u,-r同cp

12. **top** 查看系统进程的动态运行情况
	- 按1 在整体和各个cpu之间切换
	- 按P 要求top按cpu占用率排序
	- 按M 要求top按内存(RES)占用排序
	- `top [-d number] | top [-bnp]`
	- -d 表示top命令显示的页面更新一次的间隔
	- -p 指定特定的pid进程号
	

13. **ls** 查看目录信息 
	- -a 列出所有文件,包括隐藏文件
	- -l    显示文件详细信息,可简写为ll
	- -ld  显示目录详细信息
	- -lt  按时间排序显示数据信息
	- -lS  按大小排序显示数据信息
	- -lh   已可读凡是显示文件大小信息
	- -li   显示文件详细信息和索引节点号
	- -r   以相反顺序显示文件结果

14. **wc**  统计文件的字节数、字数、行数
	- -l   统计行数(lines)
	- -L 打印最长行的长度

15. **free** 显示空闲内存数量

16. **ln** 创建硬链接 `ln [源文件] [链接文件]`
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

17. **cat** 查看文本文件
	- -n 显示文件行数
	- -E 每行结束处显示"$"
	  



### 二. 文件相关命令
1. Windows与Linux文件传输:yum install lrzsz -y
	 - 上传 rz +回车
	 - 下载 sz+文件名


2. Linux与Linux文件传输:
	- 传送文件:scp  源文件地址  账号@ip:目标地址
   例: scp  /basefile  root@192.168.112.139:/opt
	- 下载文件:scp  账号@ip:目标地址  下载位置
	   例: scp  -r  root@192.168.112.138:/root/libai   /home
	   参数 -r:传送下载文件夹

3.  **wget** 资源下载
	- wget http://mirrors.aliyun.com/centios/timestamp.txt
	- 将会把文件下载到当前文件夹

4. **find** 查找文件 `find [path] [expression]`
	- -type 限制搜索文件类型
		- b,块设备文件;c字符设备文件
		- d,目录;f,普通文件;l,符号链接
	- -name 指定索索文件名,可使用通配符
	- -size  指定搜索文件大小
		- b,512字节,默认值; c,字节; M,兆; G 千兆
		- `find ~ -type f -name "*.JPG" -size +1M`
		- 查找家目录下所有大于1M的, 文件名包含.jpg普通文件
	- -delete 删除当前找到的文件
	- -newer file 指定要查找的文件创建时间新于指定文件
	- -perm mode 匹配的文件和目录的权限已经设置为指定的 mode
	- -ls 对匹配的文件执行同等与ls命令
	- 对找到的文件执行特定命令参考[[#^e3bf41|xargs]]
5. **stat** 展示文件所有信息 `stat file`
		
### 三. Linux网络命令
1. hostname  主机名  			修改主机名,临时修改
    - 永久修改:进入配置文件 /etc/hostname修改
    - hostnamectl set-icon-name "name"  永久修改主机名

2. hosts文件位置 :  /etc/hosts
	
3. netstat		查看当前网络状态信息
    - -l 显示监听的套接口
    - -n 显示端口
    - -a 所有连接和侦听端口，默认不显示LISTEN相关
    - -i 显示网络接口列表
    - -o 显示拥有的与每个连接关联的进程 ID
    - -r 显示路由表
    - -s 显示每个协议的统计信息
    - 需要下载`net-tools`工具
		
4. 测试网络连接ping 测试端口telnet

5. curl -X GET http://www.baidu.com	获取百度网页内容

6. **route** 路由表  
`需要下载net-tools工具  yum install net-tools`
	- -n查看路由表


### 四. 防火墙 firewall ,selinux服务
1. systemctl status firewalld.service	查看防火墙状态
	`systemctl is-active firewalld`
	
2. systemctl stop firewalld.service 	临时停用防火墙,start开启

3. systemctl disable firewalld.service	禁用防火墙开机启动,enable启用
	`systemctl is-enabled firewalld #查看防火墙是否永久开启关闭`

4. firewall-cmd   --reload 		重新加载防火墙

5. firewall-cmd   --zone=public  --add-port=80/tcp  --permanent	开启永久80端口(去掉--permanent为临时)

6. firewall-cmd --reload		重新载入防火墙

7. firewall-cmd   --zone=public  --remove-port=80/tcp  --permanent	关闭80端口

8. **firewall-cmd  --list-ports** 查看所有永久开放的端口(默认为空)

9. [ ] **selinux服务:** 限制root用户操作行为
	- setenforce 0 临时关闭,开启为1
	- `/etc/selinux/config` selinux配置文件
```shell
	#enforcing   开启
	#permissive  提示警告信息但不制止行为
	#disabled    关闭
	SELINUX=enforcing
```


### 五. Linux与Linux之间免密登陆
- 通过ssh命令免秘钥连接其他主机
   注:1.在~/.ssh/known_hosts记录了以前访问地址(ip hostname)信息

1. 生成秘钥
   ssh-keygen  
   私钥:id_rsa 	公钥id_rsa.pub

2. 发送公秘
   ssh-copy-id  -i  ~/.ssh/id_rsa.pub  root@192.168.112.139
   收到后保存位置 : ~/.ssh/authorized_keys

   对方收到公钥后localhost即可免密ssh登陆对方host

### 六. vim编辑器
1. 编辑模式:每个按键都有特殊含义
    快捷键:      
        gg跳转文件首,15gg跳转15行
		^跳转行首,$跳转行尾
		G跳转文件  最后一行
		dd删除一行,3dd往下数删除三行
		w跳转下个单词 dw删除当前单词,3dw删除后面三个单词
		yy复制一行,3yy往下复制三行,yw复制当前单词
		p粘贴
		u   撤销上一步的操作, . 撤销u的操作
		Ctrl+r 恢复上一步被撤销的操作
		h,j,k,l 左下上右
		ZZ 保存并退出
		r+指定单词,替换光标所在单词
		x,剪切光标所在单词
		ctrl+S锁屏 ctrl+q解锁
		
2. 输入模式: 编辑模式按a光标后(追加),按i光标插入,I,行首,A行尾,o下一行,O上一行

3. 命令模式:  编辑模式按:
   - set nu 显示行号 set nonu 取消行号
   - wq 保存并退出,q不保存退出,wq! 强制保存退出,q!强制退出
   - /libai  找到文中所有libai ,按n下一个
   -  s/libai/liqingzhao/g 把当前行所有libai替换为liqingzhao,
	   s/libai/liqingzhao/替换第一个
	   g/libai/s//liqingzhao/g 替换全局libai

### 七.时间同步测略
##### 1.网络时间同步
1. yum install ntp -y         装ntp
2. ntpdate cn.ntp.org.cn        通过cn.ntp.org.cn 设定本地时间
##### 2. 开启本地NTP服务
1. yum install ntp -y         装ntp
2. service ntpd start          开启ntpdate服务 ,开启后本机可作为一个ntp服务器供其他主机用ntpdate 命令同步时间

##### 3. timedatectl  查看系统详细
1. 修改同步时间
	- timedatectl  list-timezones   查看时区信息
	- timedatectl  set-timezone   Asia/Shanghai  设置时区为上海
	- timedatectl  set-ntp  1           开启网络时间同步功能
	- yum install chrony                  安装时间同步程序
	- systemctl start chronyd          启动chrony服务



### 八.用户-权限-组
##### 1.用户
1. 用户账号信息存储位置 `/etc/password`
	- root:x:0:0:root:/root:/bin/bash 第一行信息
	- 用户名:登陆密码(隐藏):用户id:group id:描述信息:用户home目录:用户缺省shell
2. **useradd** 创建用户 创建用户的同时会在/home下创建/username(用户的家目录)
	- useradd byhy 
	- 使用该命令创建了byhy用户和byhy组
	- useradd -g  rooy byhy
	- 创建byhy用户,并把该用户添加到root组
3. **passwd** 设置用户密码
4. **su** 切换用户
	- su - byhy
	- 切换到byhy用户
	- - 参数 ,切换用户的同时,shell工作环境会切换到用户的家目录
5. **userdel** 删除用户
6. **id** 查看用户id,gid等信息

#### 2.组
1. 组信息存储位置 `/etc/group`
2. **groupadd** 创建用户组
3. **groupdel** 删除用户组
4. **usermod** 改变用户组
	- usermod -g byhyusers byhy
	- 把byhy的组改为byhyusers
	- usermod -G g2,g3 byhy
	- 把byhy同事添加到g2,g3组
##### 三.权限
1. 目录的rwx权限表示,r 用户可以查看里面的内容,w 用户可以在目录里面创建删除文件,x 用户可以进入该目录
2. 三种类型用户分别是
	- 文件所有者,owner,也就是该文件创建者
	- 文件所有者所在同组用户,grouper
	- 其他用户,非owner和非grouper
3. **chmod** 修改文件权限 只有文件owner或root用户可以修改
	 - chmod g+w byhy.txt
	 - 对增加同组用户对byhy.txt的写入(w)权限
	 - u 拥有者(owner)
	 - g 拥有者同组用户(grouper)
	 - o 其他人(other)
	 - a 所有人(all)
	 - chmod ug-wx byhy.txt
	 - 去除拥有者和同组用户对byhy.txt的写入(w)和可执行(x)权限
	 - -R 递归的改变目录下的文件和子目录的权限
	 - chmod  -R  a+rw *
	 - 增加所有人对当前目录下所有文件的rw权限
1. **chown** 改变文件所有者 只有root用户可以改
	- chown byhy test1.txt
	- 把test1.txt的拥有者改为byhy
	- chown byhy:byhy test1.txt
	- 把test1.txt的拥有者和所属组都改为byhy
	- -R 改变目录拥有者的同时递归改变目录里面的有所文件
2. **chgrp** 改变文件用户组


### 九.进程管理
1. **ps** 查看进程信息
	- -f 显示进程详细信息
	- -e 显示所有进程信息
	- --forest  显示进程关系
2.  command & 如果command一个前台程序,加上&程序就会放到后台执行
3. **nohup** 如果希望一个进程在关闭终端后依然在后台运行,那么在启用时要加上nohup
	- 如果忘记了可以暂停程序在终端上运行`disown` 命令然后恢复之前暂停程序
1. **kill -9** 结束进程

	- kill -9 1420
	- 结束id为1420的进程

### 十.重定向和管道
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

### 十一.网络与端口
1. **ip addr** 查看网络接口
2. **ifup**       启用网络接口
3. **ifdown**  停用网络接口
4. **ping** 检查网络联通状态
5. **netstat** 查看各种与网络相关的状态信息
		包括:网络的链接,状态,接口的统计信息,路由表,端口的监听情况
		
	- -n    不显示端口协议名,显示端口数字
	- -a     (all)显示所有选项,默认不显示LISTEN相关
	- -t      (tcp)仅显示tcp相关选项
	- -p     显示建立相关链接的进程PID
	- -r      显示路由信息,路由表
6. **nmtui**  修改网卡配置,类图化界面
7. **systemctl restart network** 重启网络服务

8. `/etc/sysconfig/network-scripts/ifcfg-ens33` 网卡配置文件
```shell
TYPE=Ethernet     #设置网络类型
BOOTPROTO=none    #是否自动获取ip none/static-需要手动设置
                  #dhcp         ip地址自动获取
                  
DEFROUTE=yes      #是否激活静态默认路由条目,如果没有激活主 
                  #机不能访问外网
                   
NAME=ens33        #网卡名
UUID=ff4192c2-94c9-40bf-8875-82124a1b6462
DEVICE=ens33      #网卡名
ONBOOT=yes        #是否激活网卡状态
IPADDR=192.168.112.138
PREFIX=24
GATEWAY=192.168.112.2
DNS1=114.114.114.114
```

10. 修改网卡名称
	- 找到需要修改的网卡名,修改网卡配置文件名
	- 修改配置文件中NAME,DEVICE对应的网卡名
	- 找到`/etc/default/grub` 中`GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet"`条目
	- 在这条中加入`net.ifnames=0  bisodevname=0`
	- 进入/boot/grub2目录,运行`grub2-mkconfig -o grub.cfg` 重新生成GRUB配置,并更新内核
	- 完成后重启系统

11. `/etc/resolv.conf`  DNS服务器配置文件
	`nameserver 114.114.114.114`
	


### 十二.打包和压缩,常用工具
1. **tar** Linux上常用打包压缩解压工具
	- tar  cfv   pass.tar   byhy   t1.py   t2.py
	- 将byhy目录和t1.py,t2.py 打包成pass.tar文件
	- tar  xvf  pass.tar
	- 将pass.tar解压到当前目录
	- tar  tvf  pass.tar
	- 查看包里的内容
	- tar  rvf  pass.tar  t3.txt
	- 将t3.txt 添加到pass.tar包里

2. **gzip** 压缩和解压
	- gzip abc.txt
	- 生成一个名为abc.txt.gz 的压缩文件
	- gzip -d abc.txt.gz 
	- 解压gz压缩包
	- -c 把输出写入到标准输出，并且保留原始文件
	- -number  设置压缩指数,1(最快,最小压缩)~9(最大,最慢压缩) 

3. 打包并压缩
	- gzip和tar联合使用
	- tar  zcvf  pass.tar.gz   byhy   t1.py   t2.py
	- 将byhy目录和t1.py  t2.py 打包成pass.tar.gz文件,并压缩
	- tar  zxvf  pass.tar.gz
	- 解压并解包 pass.tar.gz 文件

4. **yum** 软件包管理工具
	- yum install package1 安装指定安装安装包package1
	- yum list                       显示已安装和可以安装的软件包
	- yum list installed         显示已安装软件包
	- yum remove package1  删除程序包package1
	- yum -y install psmisc  --downloadonly --downloaddir=/tmp
	 `下载指定软件包,--downloadonly只下载不安装,--downloaddir指定下载目录`

5. **tgz**软件安装流程
	- 没有gcc的先安装gcc yum install gcc
	- 解压代码包tar zxf python-3.6.8.tgx
	- 进入解压后的目录 cd python-3.6.8
	- ./configure
		`./configure --prefix=/usr/local/test #安装后的所有资源文件都会被放在/usr/local/test目录中`
	- 配置环境
	- make && make install
	- 编译和安装(&&表示make执行完后执行make install)
	- 通常安装在/usr/local/bin目录

6. bash-completion 补全服务名称,通过yum下载

7.  **pstree** 查看进程树
	- yum -y install psmisc 



### 十三.磁盘相关
1. **mount** 于挂载Linux系统外的文件。
	- mount  /dev/hda1 /mnt
	- 将 `/dev/hda1` 挂在 `/mnt` 之下。
	- mount -o ro /dev/hda1 /mnt
	- 将 `/dev/hda1` 用只读模式挂在 `/mnt` 之下。

2. **umount** 取消挂载
	- umount -v /dev/sda1
	- 通过设备名卸载,-v显示详细信息
	- umount -v /mnt/mymount/
	- 通过挂载点卸载

3. **fdisk** 查看磁盘信息与分区设置
	- -l 显示磁盘详细信息

4. **df**   查看磁盘挂载情况/查看磁盘使用情况
	- -h  以人类可读方式显示磁盘容量大小

5. 自动挂载
	- `/etc/fstab` 编辑该文件,将已经挂载的磁盘写入该文件
```shell
	UUID=ba0..4f45e /    xfs  defaults   0 0
	/dev/sdb3      /mnt  xfs  defaults   0 0
	# 第一列表示储存设备文件信息,可用uuid信息代替
	# 第二列表示文件挂载时指定的挂载目录
	# 第三列表示文件系统类型  `xfs:推荐,效率高  ext3,ext4:保证数据安全性`
	# 第四列表示挂载参数配置
	# 第五列表示是否具有自动保存机制
	# 第六列表示是否开启磁盘自检机制
```



### 十四.其他高级命令
1. **xargs** 从标准输入接受输入，并把输入转换为一个特 定命令的参数列表 ^e3bf41
	1. 参数说明
		- -i 将结果 一项项复制给{}
		- –null 当接受的参数中有空格是也能正常处理
	1. 实例
```shell
$ grep -rl "lubenwei" | xargs -i cp {} ./dir
## 找所有包含lubenwei的文件的文件路径, 复制到dir目录下
```


2. `uname-v`   查看系统版本

3. `export [-fnp] [变量名]=[变量值]`
	- -f 表示变量名为函数名
	- -n 暂时删除指定变量, 不会输出到后续指令的执行环境(只在子shell中有用)
	- -p 列出所有环境变量