#### 1. IP报文结构
![[屏幕截图 2024-02-19 102639.png]]

##### 字段解析
- 版本（version）：4bits，标识IP协议版本，分别为IPv4和IPv6
- 报头长度（Header Length）：4bits，标识报头的长度，单位为32bits（4字节），`四位二进制为1111=15，所以IP报头最大是15*32bits=60bytes，最小为20字节`
- 差分服务字段（Differentiated Services Field）：8bits，表示数据包优先级，主要用于QoS技术
- 总长度（Total Length）：16bits , 表示IP报文的总长度，单位是8bits，所以一个IP报文最大长度是`65535`字节
- 标识符（identification）：16bits，数据包的一个ID编号，用于表示数据包，用于数据包分片技术中
- 标志位
	- ①（Reserved bit）：1bit，保留位
	- ②（Don‘t fragment）:1bit，不可分片位（DF位），如果为1则此数据包不可分片
	- ③（More frament）:1bit，更多分片位置（MF位）如果数据包被分片，则除了最后一个分片报文的MF位是0外，其他的都是1
- 分段的偏移（Fragment Offset）：13bits，单位是8bits，标识分片报文相对于原始报文起始位置的偏移量
- 生存时间（Time to live）：8bit，初始数据包会被设置一个TTL值，每经一次路由TTL值就会减一，TTL代表了数据包被路由的次数，网络中一般称为跳数  ，当TTL减为0，网络设备就会丢弃此数据包
- 协议号：（Protocol）：8bits，标识网络层之上使用的那种封装协议，其中`TCP=6,UDP=17`
- 报头校验和（Header Checksum）：16bits，针对IP报头纠错字段，一定程度防止IP报头数据错乱
- 源IP地址（Source Address）：32bits，数据包发送方IP地址
- 目的IP地址（Destination Address）：32bits，数据包接收方IP地址
- 可选项&填充项（Options & Padding）:0-40字节，一些可选项，主要用于测试，不足32bits用0补充