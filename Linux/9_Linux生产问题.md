1. centos重启后没有网络
	1. 检查IP配置文件正常
	2. `ip addr` 命令查看网卡没有启动 , `ifup` 启动网卡提示`connection activation failed ...`
	3. 因为是手动配的ip,检查`NetworkManager`服务 , 发现该服务正在启动的那个 ,停止该服务 ,`ifup`重新启用网卡成功
	4. 新知识:network service管理网卡的配置,包括设备启动时网络设备的初始化 , networkmanager服务管理系统网络连接
	