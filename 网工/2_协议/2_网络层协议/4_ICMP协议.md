1. 简介：ICMP（Internet Control Message Protocol）因特网控制消息协议，是TCP/IP协议族的一部分
2. 作用：在网络中传输控制消息，例如网络是否可达到

>注：ICMP协议未使用传输层协议，直接封装在网络层之上，其中IP Protocol字段=1

![[屏幕截图 2024-03-26 151305.png]]
>注：Type=8，Code=0 对应Echo request报文 ，含义是回显请求（Ping请求）。Checksum：数据校验和，Ldentifier：ICMP报文的标识符，每一次Ping测试，发送方都会随机产生一个ID，Sequence Number：序列号，用于表示Ping报文的先后顺序


3. `Ping` 命令常用参数（华为网络设备上）
	- -a 指定发送Ping报文的源IP地址，默认为报文出接口IP地址
	- -c count ， 发送报文的个数，默认5个
	- -s size，发送Ping报文大小，默认5个
	- -m 发送Ping报文之间的时间间隔，默认500ms
	- -t 超时时间，默认2000ms
	- -f 不允许分片