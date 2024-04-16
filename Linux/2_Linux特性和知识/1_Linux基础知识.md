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
	- /usr                             存储系统默认软件(UNIX software Resource UNIX软件资源)
	- /proc                           虚拟文件系统 , 放置的数据在内存中 , 不占用任何硬盘空间 , 存放系硬件信息/系统内核配置信息
	- /tmp                            数据临时存储
	- /lib                               系统或软件服务程序库文件存储目录
	- /lib64                            系统或软件服务程序库文件存储目录
	
3. 权限  查看文件详细信息 :-  rwx  rw-  ---
	- 第一个- 表示为这时一个普通文件,d表示文件夹,l表示这是一个连接
	- rwx表示对文件或目录的权限r:读取,w:写入,x:可执行
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
	- 查看可用系统编码 `locale -m`

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

12. 查看系统版本 `cat /etc/os-release`

13. 免密登录
	1. `ssh-keygen -t [rsa|dsa]` 生成公钥或私钥id_rsa，id_rsa.pub或id_dsa，id_dsa.pub（位于$HOME）下
	2. 将`.pub` 文件复制到被登录机器的家目录下`.ssh`文件夹下的 `authorized_keys` 文件中，可以使用如下命令：
		- `ssh-copy-id -i ~/.ssh/id_rsa.pub user@server` 
		- `cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"` 
	3. 修改服务器文件权限 , 否则SSH服务器可能会拒绝读取该文件
		- `chmod 700 ~/.ssh/`
		- `chmod 600 ~/.ssh/authorized_keys`

