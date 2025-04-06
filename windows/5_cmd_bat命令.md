1. cmd命令设置静态IPv4地址
netsh interface ip set address   网卡名称  static   静态ip  子网掩码   默认网关
例：netsh interface ip set address "以太网" static 10.154.99.4 255.255.255.0 10.154.99.1

2. cmd命令设置自动获取IPv4地址
netsh interface ip set address name=网卡名称  source=dhcp

3. cmd命令设置静态IPv6地址
netsh interface ipv6 set address 网卡名称 静态ip

4. cmd命令设置静态IPv6网关
netsh interface ipv6 add route ::/0 网卡名称  网关

5. pause
暂停,按任意按键退出cmd

6. >>文件路径 文件名,将指定内容保存到指定路径的文件名
例:ipconfig >> D://ip地址备份.txt

7. echo 打印 
参数 off 此语句后所有运行的命令都不显示命令行本身，但是本身的指令是会显示出来的

8. @ 不显示本行命令行
例:@echo off

9. call 调用另一个批处理文件

10. rem 表示此命令后的字符为解释行

11. 中文乱码问题
	 - chcp 声明或更改代码页
	- 65001:UTF-8    936:GB2312    20127:US-ASCII     例chcp  65001
	- 默认情况下，在 bat 脚本文件中，如果中文不是ANSI编码，就会出现乱码,可以另存为的时候编码方式选择为`ANSI`编码

12. del 删除一个或数个文件
	/P 删除每一个文件之前提示确认。/F 强制删除只读文件。/S 从所有子目录删除指定文件。
	/Q 安静模式。删除全局通配符时，不要求确认。/A 根据属性选择要删除的文件

13. rd 删除目录
	/S  除目录本身外，还将删除指定目录下的所有子目录和文件。用于删除目录树
	/Q      安静模式，带 /S 删除目录树时不要求确认

14. route print 查看路由表格

15. certutil -hashfile 文件名 MD5 查看md5
	certutil -hashfile 文件名 SHA1 查看SHA1
	certutil -hashfile 文件名 SHA256 查看SHA256

16. **for 参数 \%\%变量  in (相关文件或命令) do 执行的命令** for循环
	- 在cmd窗口时,变量一个%,写成bat时变量\%\%
	- 变量是形式变量,不可省略
	- for依次提取()中每一个元素,把他赋值给变量,带到do后参与命令执行

17. **chkdsk** 磁盘修复工具

18. 修改MTU的值
	- `netsh interface ipv4 show subinterfaces` 查看MTU的值
	- `netsh interface ipv4 set subinterface "wlan" mtu=1500 store=persistent` 修改网卡wlan的值为1500
19. ping
	- -l 指定数据包大小（一旦size超过1472字节可能ping不通，服务器处于安全防护）（ping自身报文长度为8bit，ip报头长度为20字节，8+20+1472=1500，大部分服务器响应报文大小为1500）
	- -f 数据包能被分片
### 实例:
1. 查看局域网内所有IP与Mac地址
	- ipconfig /all 查看本机的首选IP地址
	- for /L %i IN (1,1,254) DO ping -w 1 -n 1 192.168.3.%i
	   注意：该命令中输入的IP地址前3位必须与本机的IP地址相同。
	- arp -a

2. windows11 安装跳过联网激活
	1. 进入OOBE界面，按`shift+F10` 打开终端（笔记本可能要按`fn+shift+f10`）
	2. 输入`devmgmt.msc` 打开设备管理器，禁用网卡驱动
	3. 输入`oobe\bypassnro` 等待重启
	4. 如果`oobe\bypassnro` 命令失效，可输入`reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\OOBE /v BypassNRO /t REG_DWORD /d 1 /f shutdown /r /t 0` 

