#### 1. 简介
1. ansible是基于 paramiko 开发的,并且基于模块化工作，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。ansible不需要在远程主机上安装client/agents，因为它们是基于ssh来和远程主机通讯的

2. ansible主要模块
	- `Ansible`: Ansible核心程序
	- `HostInventory`: 记录由Ansible管理的主机信息，包括端口、密码、ip等。
	- `Playbooks`: “剧本”YAML格式文件，多个任务定义在一个文件中，定义主机需要调用哪些模块来完成的功能。
	- `CoreModules`: 核心模块，主要操作是通过调用核心模块来完成管理任务。
	- `CustomModules`: 自定义模块，完成核心模块无法完成的功能，支持多种语言。
	- `ConnectionPlugins`: 连接插件，Ansible和Host通信使用

3. ansible任务执行模式
	- `ad-hoc` 模式(点对点模式) : 使用单个模块，支持批量执行单条命令。ad-hoc 命令是一种可以快速输入的命令，而且不需要保存起来的命令。就相当于bash中的一句话shell。
	- `playbook`模式(剧本模式): 主要管理模式 , playbook通过多个task集合完成一类功能，如Web服务的安装部署、数据库服务器的批量备份等。可以简单地把playbook理解为通过组合多条ad-hoc操作的配置文件。

4. ansible 命令执行过程
	1. 加载自己的配置文件，默认`/etc/ansible/ansible.cfg`；
	2. 查找对应的主机配置文件，找到要执行的主机或者组；
	3. 加载自己对应的模块文件，如 command；
	4. 通过ansible将模块或命令生成对应的临时py文件(python脚本)， 并将该文件传输至远程服务器；
	5. 对应执行用户的家目录的`.ansible/tmp/XXX/XXX.PY`文件；
	6. 给文件 +x 执行权限；
	7. 执行并返回结果；
	8. 删除临时py文件，`sleep 0`退出；


#### 2. 安装
1. `pip` 安装
```bash
	yum install python-pip
	pip install ansible
```

2. `yum` 安装
```bash
	yum install epel-release -y
	yum install ansible –y
```

3. 安装目录如下(yum安装)
	- 配置文件目录：/etc/ansible/  
	- 执行文件目录：/usr/bin/  
　　- Lib库依赖目录：/usr/lib/pythonX.X/site-packages/ansible/  
　　- Help文档目录：/usr/share/doc/ansible-X.X.X/  
　　- Man文档目录：/usr/share/man/man1/

4. ansible配置文件查找顺序
	1.  检查环境变量`ANSIBLE_CONFIG`指向的路径文件(export ANSIBLE_CONFIG=/etc/ansible.cfg)；
	2.  `~/.ansible.cfg`，检查当前目录下的ansible.cfg配置文件；
	3. `/etc/ansible.cfg`检查etc目录的配置文件

5. ansible配置文件一般为 `/etc/ansible/ansible.cfg` , 以下配置文件中常见参数
```
	inventory = /etc/ansible/hosts		#主机清单inventory文件的位置
	library = /usr/share/ansible		#指向存放Ansible模块的目录，支持多个目录方式，只要用冒号（：）隔开就可以
	forks = 5		#并发连接数，默认为5
	sudo_user = root		#设置默认执行命令的用户
	remote_port = 22		#指定连接被管节点的管理端口，默认为22端口，建议修改，能够更加安全
	host_key_checking = False		#设置是否检查SSH主机的密钥，值为True/False。关闭后第一次连接不会提示配置实例
	timeout = 60		#设置SSH连接的超时时间，单位为秒
	log_path = /var/log/ansible.log		#指定一个存储ansible日志的文件（默认不记录日志）
```

6. 主机清单 ,保存 ansible 需要连接管理的主机列表
```text
1. 直接指明主机地址或主机名
	## green.example.com#
	# blue.example.com#
	# 192.168.100.1
	# 192.168.100.10
2. 定义一个主机组[组名]把地址或主机名加进去(组成员可以使用通配符来匹配)
	[mysql_test]
	192.168.253.159
	192.168.253.160
	192.168.253.153
```


#### 3. 使用
