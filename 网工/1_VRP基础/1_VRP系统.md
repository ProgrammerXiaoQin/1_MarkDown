- - - 
#### 0. 系统介绍
1. VRP(Versatile Routing Platform) 通用路由平台 , 华为网络设备操作系统 , 这些网络设备包括但不限于路由器 , 交换机 , 防火墙 等等

2. 设备管理接口 , 一般企业网络设备上都有提供Console接口(接口类型为RJ45网线接口)
	- console口登录一般用console线(一端为console口一端为COM口)链接电脑 , 但是现在大部分计算机不带COM口 , 所以我们还需一根usb口转COM口线 

3. 设备链接方式 , 用console线链接设备后 , 可以用xshell , putty等终端链接软件链接设备
	- 链接方式选择 Serial(串口)

4. 系统启动相关
	1. `display startup` 查看系统启动信息
	2. `startup system-software [系统软件完整路径]` 修改下次系统启动软件
	3. `startup saved-configuration [flash:/xxxx.zip]` 修改下次启动加载的配置文件

5. 系统配置文件相关
	1. `save` 保存配置文件 , `save name.zip` , 保存配置文件为name.zip文件

#### 1. 命令行视图
1. 用户视图 , 查看运行状态或其他参数
	- `return` 直接退回到用户视图 , 也可以使用CTRL+Z
	- `dir`  查看系统配置文件的存储使用
	- `system-view` 切换系统视图
2. 系统视图 , 配置设备系统参数等
	- `sysname [name]` 设置系统名称
	- `clock timezone [String] add 8` 修改时区为东8区
3. 接口视图 , 配置接口参数
	- `display this` 查看当前接口下的配置
4. 协议视图 , 配置路由协议

#### 2. 命令行功能
1. CTRL+A 光标移到命令行最前
2. CTRL+C 停止当前命令运行
3. CTRL+E 光标移当前行的末尾
4. CTRL+W 删除一个单词
5. CTRL+Z 退回用户视图
6. CTRL+] 终止当前链接或切换链接
   
#### 3. 常用命令
1. `quit` 退回到上一个视图
2. `sysname [name]` 修改设备名
4. `reboot` 重启设备


#### 4 .查看系统信息
1. `display [commend]`
	1. 系统配置相关
		- `clock` 系统时间
		- `version` 查看路由信息
		- `current-configuration` 查看当前配置(内存中)
		- `saved-configuration` 查看保存的配置(flash中)
	2. 接口相关
		- `interface g0/0/0` 查看接口信息
		- `ip interface brief` 查看全部接口ip简要信息 , 去掉IP为查看二层接口信息
	3. 其他
		- `display user-interface` 查看用户信息,包括登录等级等等
		- `users` 查看当前登录用户信息
		- `acl all` 查看所有acl规则
		- `ip routing-table` 查看路由表 ,后面跟IP可以查看指定想要看到路由表条目
		- `tcp status` 查看tcp状态, 信息
#### 5. 系统基础设置
1. 设置console链接设备密码
	- `user-interface console 0` 进入console 0 接口
	- `authentication-mode password` 设置密码 , 也可以使用`set authentication password cipher`命令修改

2. 配置Telnet远程登录
	1. 启动服务
		- `telnet server enable` 启动telnet服务
	2. 配置用户接口视图
		- `user-interface vty 0 4` 进入 虚拟用户终端vty 0-4 接口
		- `protocol inbound telnet` 允许用户使用telnet登录(默认允许)
		- `authentication-mode password` 修改认证模式为密码认证 , 回车设置密码
		- `set authentication password cipher` 修改密码
	3. 设置登录用户等级
		- `user privilege level 3` 设置用vty方式登录系统用户等级为3
	4. 其他设置
		- `user-interface maximum-vty ?` 系统视图下设置最大远程登录系统用户数
		- `idle-timeout 60` 用户视下设置远程登录超时时间为60分钟

3. 设置SSH登录模式
	1. 启动ssh服务
		- `stelnet server enable` 启用stelnet服务
		- `display ssh server status` 查看ssh服务状态
	2. 生成RSA密钥对
		- `rsa local-key-pair create`  生成密钥对 
		- `display rsa local-key-pair public` 查看公钥
	3. 配置vty远程登录
		- `user-interface vty 0 4` 进入 虚拟用户终端vty 0-4 接口
		- `protocol inbound ssh` 修改协议入站方式为ssh
		- `authentication-mode aaa` 修改认证模式为aaa认证
	4. 其他设置
		- `ssh server port [port]` 修改ssh默认端口
		- `ssh server timeout ?`  设置认证超时时间

4. 用户管理 , 创建用户 , 指定用户等级和用于的服务
	- `aaa` 在系统视图下 , 进入aaa视图
	- `local-user admin password cipher huawei` 设置本地用户名为admin , 密码为 : huawei
	- `local-user admin privilege level 3` 修改用户等级为3
	- `local-user admin service-type serviceName` 设置admin用户用来进行的服务类型

5. 系统启动
	1. `startup system-software [系统软件完整路径]` 修改下次系统启动软件
	2. `startup saved-configuration [flash:/xxxx.zip]` 修改下次启动加载的配置文件
	
7. `sysname` 修改系统名
8. `clock timezone CST add 8` 修改时区为东8区
9. `clock datetime` 设置时间和日期

#### 6. 实用命令
1. `compare configuration vrpcfg.zip` 比较当前配置和保存配置区别
2. `super password level 15 cipher [密码]`  设置一个级别为15的超级密码
	- `super 15` 使用超级密码切换权限到15

#### 7. 文件系统
1. `dir`  查看当前目录文件 (vrp系统文件根目录为`flash:`)
2. `pwd`  查看当前路径
3. `cd`  更改当前路径
4. `mkdir`  创建目录
5. `rm`  删除文件
6. `copy` 复制文件
7. `rename` 重命名
8. `move` 移动文件
9. `delete` 删除文件
10. `undelete` 恢复文件
11. `reset recycle-bin` 清空回收站