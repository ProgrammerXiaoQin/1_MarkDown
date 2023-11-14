1. centos重启后没有网络
	1. 检查IP配置文件正常
	2. `ip addr` 命令查看网卡没有启动 , `ifup` 启动网卡提示`connection activation failed ...`
	3. 因为是手动配的ip,检查`NetworkManager`服务 , 发现该服务正在启动的那个 ,停止该服务 ,`ifup`重新启用网卡成功
	4. 新知识:network service管理网卡的配置,包括设备启动时网络设备的初始化 , networkmanager服务管理系统网络连接

2. 诊断和排除Linux服务器上的I/O性能问题
	1. `lsblk` 查看系统上的存储设备 
	2. `top`  看wa(CPU等待百分比) , 基本上是CPU等待I/O操作完成的时间 , 如果高于`10%`或`20%` 那么基本就是服务器卡顿瓶颈与I/O操作有关
	3. `iostat -hymx 1 4` 查看设备性能 , 主要看 `%util` 项 , 如果过高基本可以判断为I/O问题
	4. `iotop -o`  查看哪些进程在使用磁盘上的I/O

3. centos使用sudo命令时候报错`# XXXX is not in the sudoers file. This incident will be reported`
	1. 使用root账号 , 修改`/etc/sudoers`文件
	2. 在文件中添加以下四行其中一行
```text
minghai           ALL=(ALL)             ALL 

%minghai          ALL=(ALL)             ALL

minghai           ALL=(ALL)             NOPASSWD：ALL

%minghai          ALL=(ALL)             NOPASSWD：ALL
```

第一项: 允许用户minghai执行sudo命令(需要输入密码)
第二项: 允许许用户组minghai里面的用户执行sudo命令(需要输入密码)
第三项:允许用户minghai执行sudo命令，并且在执行的时候不输入密码
第四项:允许用户组minghai里面的用户执行sudo命令，并且在执行的时候不输入密码。

4. centos切换用户时报错`This account is currently not available`
	1. `cat /etc/passwd | grep 用户名`
	2. 将末尾shell `/sbin /nologin`修改为`/bin/bash`
	3. 保存退出