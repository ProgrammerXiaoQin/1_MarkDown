1. 简介 : ==Internet Control Message Protocol== 因特网控制消息协议 , 是TCP/IP协议族重要组成部分 , ping命令使用ICMP协议

2. 作用 : 在网络中传输控制消息 , 例如网络是否可达 (注 : ICMP并未使用传输层协议 , 而是直接封装在网络层之上 , 其IP Protocol字段`==`1)

3. 报文封装结构 : `|数据链路层 | 网络层 | ICMP协议|` 

