1. 简介：ARP（address Resolution Protocol）地址解析协议
2. 作用：根据IP地址解析出对应MAC地址

3. 原理：
	1. AR1从接口G0/0/0发送一个ARP广播报文，向此网段中所有主机询问10.1.1.2的MAC地址
	2. 若某一台主机的IP地址为10.1.1.2，则此主机会响应此ARP请求，告知对应MAC
	3. 其他主机忽略请求
	4. AR1获取到10.1.1.2的IP地址

4. 使用
	1. 网络设备查看arp表
		- 华为：`display arp`
		- Linux：`arp`
		- windows：`arp -a`

