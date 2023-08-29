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

9. **selinux服务:** 限制root用户操作行为
	- setenforce 0 临时关闭,开启为1
	- `/etc/selinux/config` selinux配置文件
```shell
	#enforcing   开启
	#permissive  提示警告信息但不制止行为
	#disabled    关闭
	SELINUX=enforcing
```
