### 一.常用命令和工具
1. **dpkg** Debian Linux系统上安装、创建和管理软件包
	- -i   安装软件包
	- -r   删除软件包
	- -p  删除软件包的同时删除其配置文件
	- -l   已安装软件列表

2. sudo
	**sudo + command** 用管理员身份执行command
	- -s 以后的所有命令都是管理身份执行
	- -i 切换为root用户

3. **lscpu** 查看cpu信息,Architecture指架构
	-   ARM架构：aarch64、arm64
	-   X86架构：x86_64、x64、AMD64
	-   MIPS架构：mips

4. **top** 查看系统进程的动态运行情况
	- 按1 在整体和各个cpu之间切换
	- 按P 要求top按cpu占用率排序
	- 按M 要求top按内存(RES)占用排序
	- - top -p pid
	- 查看单个进程运行情况

5. **free** 
	- free -m
	- 已兆为单位查看内存使用情况
	

#### 二.常用操作
1. 麒麟系统忘记密码
	- 重启电脑进入Grub引导菜单,选择第一行,按e进入编辑
	- 选到Linux开头这一行,光标移到最后,删除到ro,把ro改成rw,继续输入 **single console=tty init=/bin/bash** 我这里最后的security=kysec保留不影响后面操作，当然有些机器的原因，可以选择删除掉。然后按键盘的F10启动进入单用户模式。
	- 输入 passwd+对应账号名 连续输入两次密码（两种字符，8位以上），并且密码是加密不提示的，输入完成按下回车键，提示password updated successfully就是密码修改完成了，如果提示其他可以根据提示重新进行操作。
	- 退出单用户模式`exec /sbin/init`
1. 麒麟系统禁用usb存储
	- 在 **/etc/modprobe.d** 中创建一个名为**block_usb.conf**的文件
	- 在文件中添加以下内容 **install usb-storage /bin/true**
	- 保存退出
2. 银河麒麟系统镜像还原
	- 进入u盘系统后在终端中输入
	  ```
	  sudo -s
	  fdisk -l /dev/sd* 
	  ```
	- 一般显示最后一个设备为u盘,输入`mount /dev/sdc1 /mnt` 进行挂载(注意,这里的/sdc1根据实际设备获取,可能不是/sdc1)
	- 回到桌面进行安装 u盘挂载在/mnt文件夹
4. 麒麟系统防火墙
	- **ufw disable** 永久关闭
	- **ufw stop** 临时关闭
	- **ufw enable** 永久开启
	- **ufw start** 临时启用
	- **ufw default allow/deny** 外来访问默认允许/拒绝
	- **ufw allow/deny 20** 允许/拒绝访问20端口,20后可跟/tcp或/udp,表示tcp或udp封包
	- **ufw allow/deny servicename:ufw** 从/etc/services中找到对应services的进行过滤
	- **ufw allow proto tcp from ip/端口 to 本机ip port 25** 允许指定ip/端口的tcp封包访问本机25端口
	- **ufw delete allow/deny 20** 删除以前定义"允许/拒绝访问20端口"的规则 
5. 麒麟系统禁用网卡,注**wlp2s0** 为网卡名
	- 打开 /etc/network/interfaces
	- 在文件中添加,
	```
	source-directory /etc/network/interfaces.d
	iface wlp2s0 inet manual
	```
	- 重启网络服务**systemctl restart NetworkManager**
6. 麒麟系蓝牙模块
	- **systemctl stop bluetooth** 临时关闭
	- **systemctl disable bluetooth** 永久关闭
	- **systemctl start bluetooth** 临时启用
	- **systemctl enbale bluetooth** 永久启用

7. 进入文本模式
	1. 未开机的情况下进入grub菜单,按e,在打开的页面中在quiet后面输入`text` , 再按ctrl+x等待加载完后进入tty1模式
	2. 已开机的情况下按ctrl+alt+F1~F6之间切换
#### 二.开机自动执行任务
- `/etc/rc.d/rc.local` 修改目录,给该目录添加执行权限





#### 三.分区


