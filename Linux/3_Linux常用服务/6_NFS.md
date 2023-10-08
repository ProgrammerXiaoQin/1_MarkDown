- - - 
#### 1. 简介
1. NFS(Network File System) , 主要功能是 : 通过网络、让不同的机器、不同的OS可以共享彼此的文件
2. NFS可以允许NFS客户端将远端NFS的共享目录挂载到自己的NFS客户端，这样客户端就好比有在本地有一块磁盘一样，只不过是网络磁盘而已
3. NFS跟RPC会使用 `tcp 111 2049 udp 111 4046` 端口 

#### 2. 通信过程
1. 首先服务器端启动RPC服务，并开启111端口

2. 服务器端启动NFS服务，并向RPC注册端口信息

3. 客户端启动RPC服务，向服务端的RPC服务请求服务端的NFS端口

4. 服务端的RPC服务反馈NFS端口信息给客户端。

5. 客户端通过获取的NFS端口来建立和服务端的NFS连接并进行数据的传输

#### 3. 安装部署
1. `rpm -qa | grep nfs-utils` 查看是否安装 , 没有安装使用`yum`安装

2. `systemctl status rpcbind` 查看rpcbind服务是否启动 , 没有则启动

3. `systemctl enable --now nfs-server` 启动NFS服务并设置为开机自动启动

4. `vim /etc/exports` 编辑配置文件
	- `/root/nfs_data 192.168.1.* (rw,sync,root_squash)`
	- `/root/nfs_data` 共享的目录
	- `192.168.1.*` 允许该ip地址范围内所有主机访问NFS共享资源文件夹
	- `rw` 读写 , `ro` 只读
	- `sync`  同步模式 , 内存数据实时写入磁盘 , 会降低磁盘效率  
	- `async`  非同步模式 , 每隔一段时间将内存数据写入磁盘 , 保证磁盘效率 , 缺点是断电会丢失部分数据
	- `no_root_squash` 客户端挂载NFS共享目录后，root用户不受约束，权限很大，客户端的root用户不受限制，可以像访问本地文件一样访问服务端的共享文件
	- `root_squash`  客户端上的root用户收到约束，被限定成某个普通用户
	- `all_squash`  客户端上所有用户在使用NFS共享目录时都被限定为一个普通用户

5. `systemctl restart nfs-server` 重启服务

6. 客户端使用
	- `yum install -y nfs-utils` 安装NFS
	- `showmount -e 192.168.1.74` 检查是否有访问服务端的权限
	- `mount -t nfs 192.168.1.74:/root/nfs_data /root/nfs_data` 挂载