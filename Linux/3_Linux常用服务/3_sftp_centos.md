 
#### 0. 简介
1. FTP是一种在互联网中进行文件传输的协议，基于客户端/服务器模式，默认使用20、21号端口，其中端口20用于进行数据传输，端口21用于接受客户端发出的相关FTP命令与参数, ftp协议有主动模式和被动模式两种工作模式

2. vsftpd是一款开源免费的ftp服务器软件 , 支持虚拟用户 , 限制带宽等 ,vsftpd作为更加安全的文件传输协议服务程序，允许用户以3种认证模式登录FTP服务器
	- `匿名开放模式`：是最不安全的一种认证模式，任何人都可以无须密码验证而直接登录到FTP服务器
	- `本地用户模式`：是通过Linux系统本地的账户密码信息进行认证的模式，相较于匿名开放模式更安全，而且配置起来也很简单。但是如果黑客破解了账户的信息，就可以畅通无阻地登录FTP服务器，从而完全控制整台服务器
	- `虚拟用户模式`：更安全的一种认证模式，它需要为FTP服务单独建立用户数据库文件，虚拟出用来进行密码验证的账户信息，而这些账户信息在服务器系统中实际上是不存在的，仅供FTP服务程序进行认证使用。这样，即使黑客破解了账户信息也无法登录服务器，从而有效降低了破坏范围和影响

#### 1. 安装
1. 安装 `yum install -y vsftpd`
2. 启动`systemctl start vsftpd.service`
	- 开机自启动`systemctl enable vsftpd.service`
>执行该命令时如果提示错误信息Job for vsftpd.service failed because the control process exited with error code，请排查是否存在下述问题:网络环境不支持IPv6时，运行命令vim /etc/vsftpd/vsftpd.conf将内容listen_ipv6=YES修改为listen_ipv6=NO。 MAC地址不匹配时，运行命令ifconfig查看MAC地址，并在/etc/sysconfig/network-scripts/ifcfg-xxx配置文件中新增或修改HWADDR=<MAC地址>

3. 查看ftp服务监听端口 `netstat -anp | grep ftp`

#### 2. 配置
>为保证数据安全，本文主要介绍被动模式下，使用本地用户访问FTP服务器的配置方法
1. 为FTP服务创建一个Linux用户 , 本示例中，该用户名为ftpuser
```text
 adduser ftpuser
```
>若提示useradd：警告：此主目录/用户已经存在则说明之前已经存在该用户，这时你要判断是否需要手动删除。若需要删除,则执行该命令：> userdel -r rftpUser 重点是 -r 加上这个才能删除掉,否则没权限

2. 修改ftpuser密码 `passwd ftpuser`

3. 创建一个供FTP服务使用的文件目录, 及搭建的FTP服务用于存储数据的位置
```text
mkdir /var/ftp/ftpuser
```

4. 修改ftp文件目录拥有者
```text
chown -R ftpuser:ftpuser/var/ftp/ftpUser
```

5. 修改vsftpd.conf配置文件
```shell
vim /etc/vsftpd/vsftpd.conf
```

6. 除了下面提及参数 , 其他保持默认值即可
```text
anonymous_enable=NO      #禁止匿名登录FTP服务器。
local_enable=YES         #允许本地用户登录FTP服务器。
listen=YES               #监听IPv4 sockets。
write_enable=YES         #允许写入
#修改添加权限控制参数，调整上传文件具备的权限.否则使用程序操作FTP文件会因为权限问题失败
#这样配置可以授权上传文件777权限
local_umask=0
file_open_mode=0777
anon_umask=0777

# listen_ipv6=YES         #关闭监听IPv6 sockets。

#在配置文件的末尾添加下列参数：
local_root=/var/ftp/ftpuser  #设置本地用户登录后所在目录。
chroot_local_user=YES     #全部用户被限制在主目录,chroot是改变根目录的意思
chroot_list_enable=YES       #启用例外用户名单。

#指定例外用户列表文件，列表中用户不被锁定在主目录。
chroot_list_file=/etc/vsftpd/chroot_list

#开启被动模式。
pasv_enable=YES
allow_writeable_chroot=YES   #允许登陆用户有写权限

#本教程中为Linux实例的公网IP。
pasv_address=<FTP服务器公网IP地址>

#设置被动模式下，建立数据传输可使用的端口范围的最小值。
#建议您把端口范围设置在一段比较高的范围内，例如50000~50010，有助于提高访问FTP服务器的安全性。
#设置被动模式下，建立数据传输可使用的端口范围的最大值。
pasv_min_port=<port number>
pasv_max_port=<port number>
```

7. 创建chroot_list文件，并在文件中写入例外用户名单
```shell
echo 'ftpuser' >> /etc/vsftpd/chroot_list
```
>没有例外用户时，也必须创建chroot_list文件，内容可为空

8. 重启vsftpd服务:
	- `systemctl restart vsftpd.service`

#### 3. 问题
1. vsftpd启动后本机能访问但是其他机器无法访问
	1. 防火墙没有开放21端口
		- 查看防火墙端口`filewall-cmd --lsit-all`
		- 开启21端口 `firewall-cmd   --zone=public  --add-port=80/tcp  --permanent`
	2. selinux被启用
		- 查看selinux状态 `sestatus`
		- 编辑`/etc/selinux/config` 关闭selinux
	3. 可以尝试给ftp目录添加ftp目录访问权限
		- `chcon -R -t ftpd_data_t  /var/ftp/ftpuser`
2. Ubuntu系统vsftpd启动后本机能访问但是其他机器无法访问
	1. 运行上面的还是不能访问
	2. 开启20, 21端口
	3. 修改防火墙服务`ufw allow proto tcp from any to any port min_prot:max_port` 
	4. 重启防火墙服务`ufw reload`

#### 4. 配置文件说明
```shell
cat /etc/vsftpd

vsftpd.conf    #核心配置文件, 由此文件衍生出其它配置文件
chroot_list    #主要功能是可以将某些账号的使用者 chroot 在制定目录下
ftpusers       #指定的那个用户无法登入的用户配置文件
user_list      #是 vsftpd 自定义的抵挡项目
```

 这里ftpusers不受任何配制项的影响,他是个总是效黑名单, 一般用来限制账号(root、高权限账户)登录FTP, 防止登录账号权限过大 , 列表中的用户不能访问FTP ,是vsftpd服务在启动后已经决定的; ftpusers 文件只要存在, 则这个列表里面用户都不能访问FTP; 在 vsftpd.conf 配置中没有参数配置可以控制该文件存放

user_list是 `vsftpd.conf` 配置文件中的 userlist_enable 和 userlist_deny 两个配置相关 , userlist_deny 设置为 userlist_deny=NO , 那么user_list列表就变成了强制白名单;则只允许user_list文件列表内的用户访问, 拒绝其他人的访问, YES则为黑名单, userlist_enable=YES时 , userlist_deny配置项才有效, user_list文件才会被使用

1. vsftpd.conf 其他参数说明
```text
connect_from_port_20=YES|NO    YES     指定FTP使用20端口进行数据传输
listen_port=21                 21      设置FTP服务器建立连接所监听的端口
dirmessage_enable=YES|NO       YES     第一次进入目录的的.message说明
message_file=.message                  设置目录消息文件
listen=YES|NO                  YES     设置vsftpd服务器是否以standalone模式运行
pasv_enable=YES|NO             YES     是否使用PASV工作模式（被动莫模式）
```
