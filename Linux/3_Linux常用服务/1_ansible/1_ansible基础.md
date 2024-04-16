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

5. 注意事项
	- 基于模块化工作，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架
	- ansible不需要在远程主机上安装client/agents，因为它们是基于ssh来和远程主机通讯的
	- 执行ansible的主机一般称为主控端，中控，master或堡垒机
	- 主控端Python版本需要2.6或以上
	- 被控端Python版本小于2.4，需要安装python-simplejson
	- 被控端如开启SELinux需要安装libselinux-python
	- windows 不能做为主控端

