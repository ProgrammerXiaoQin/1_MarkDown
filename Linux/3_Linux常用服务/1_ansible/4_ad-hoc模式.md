1. 语法 `ansible [host_or_group] -m module_name -a 'command'`
	- `host_or_group`：您要执行操作的目标主机或主机组。
	- `-m module_name`：要执行的 Ansible 模块。
	- `-a "arguments"`：模块的参数。
	- `command` 操作

2. 常用模块
	1. `ping` 测试主机连通性
		- `ansible LES -m ping` 
	2. `command` 直接在远程主机上执行命令，并将结果返回本主机。。它不会通过shell进行处理，比如$HOME和操作如“<"，">"，"|"，";"，"&" 工作（需要使用（shell）模块实现这些功能），常用参数：
		- `chdir=dir` 在执行命令之前，先切换到该目录  
		- `executable` 切换shell来执行命令，需要使用命令的绝对路径  
		- `free_form`  要执行的Linux指令，一般使用Ansible的-a参数代替。  
		- `creates=file` 当这个文件存在，则该命令不执行,可以用来做判断  
		- `removes`  一个文件名，这个文件不存在，则该命令不执行
		- `ansible web -m command -a 'creates=/data/aaa.jpg ls'` 如果/data/aaa.jpg存在，则不执行“ls”命令
		- `ansible web -m command -a 'chdir=/data/ ls'` 先切换到/data/ 目录，再执行“ls”命令
	3. `shell` 在远程主机上调用shell解释器运行命令，支持shell的各种功能，例如管道等。
	4. `copy` 将文件复制到远程主机，同时支持给定内容生成文件和修改权限等。  
		- `src` 源路径
		- `dest` 目标路径
		- `backup` 当文件内容发生改变后，在覆盖之前把源文件备份，备份文件包含时间信息
		- `directory_mode` 递归设定目录的权限
		- `mode` 设置权限
		- `ansible web -m copy -a 'src=~/hello dest=/data/hello'`



3. 例
	1. `ansible all -i inventory_file -m shell -a "cat /etc/redhat-release"` ansible查看所有主机下的/etc/redhat-release文件
		- `ansible` 表示要使用 Ansible 工具。
		- `all` 是目标主机的模式，表示对所有主机执行操作。
		- `-i inventory_file` 指定了主机清单文件的位置，你需要将 `inventory_file` 替换为你的实际清单文件路径。
		- `-m shell` 指定了要使用的 Ansible 模块，这里我们使用了 `shell` 模块来执行 shell 命令。
		- `-a "cat /etc/redhat-release"` 指定了要执行的命令，这里是读取 `/etc/redhat-release` 文件的内容。
