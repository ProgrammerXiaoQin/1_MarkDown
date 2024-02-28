#### 1. UDP协议
1. UDP协议（User Datagram Protocol）用户数据协议，一种无连接的传输层协议，采用客户端+服务器的通信模型。无连接：数据传输前无需额外建立通信的通道，可以直接发送应用层数据。同样的，UDP需要在客户端监听一个通信端口，服务端监听一个通信端口，用于收发数据报文

#### 2. UDP报文结构
![[屏幕截图 2024-02-20 181531.png]]
##### 字段解析
- 源端口，目标端口：16bits，标识数据发送，接收方应用程序
- UDP报文总长度（Length）：16bits，标识UDP报文总长度，包含应用层部分，单位字节
- 校验和（Checksum）：16bits，用于UDP报头+应用层数据Data 的差错校验。（可选字段，如果无需校验和，则把此字段的值设置为0）

#### 3. 常用UDP知名端口号
- DNS协议（Domain Name System 域名系统）：UDP=53
- DHCP协议：UDP=67（服务端监听），UDP=68（客户端监听）
	Dynamic Host Configuration Protocol 动态主机配置协议）
- TFTP协议：UDP=69
- NTP协议：UDP=123
- SNMP协议：UDP=161,162
- RIP协议：UDP=520