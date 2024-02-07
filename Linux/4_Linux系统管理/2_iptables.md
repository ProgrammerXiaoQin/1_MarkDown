#### 1. 简介
1. iptables是防火墙用户代理  ,  用于把用户的安全规则添加到安全框架中 , 安全框架就是防火墙 , 名为netfilter , netfilter位于内核空间中 , 是Linux操作系统核心层内部的一个数据包处理模块 , iptables是用于在用户空间对内核空间的netfilter进行操作的命令行工具
2. `netﬁlter/iptables`功能 :
	- 数据包过滤，即防火墙
	- 数据包重定向，即转发
	- 网络地址转换，即可NAT
3. `iptables` 概念 : 
	- iptables是按照规则(rules)来办事的，而规则就是运维人员所定义的条件；规则一般定义为“如果数据包头符合这样的 条件，就这样处理这个数据包”。
	- 规则存储在内核空间的数据包过滤表中，这些规则分别指定了源地址、目的地址，传输协议(TCP、UDP、ICMP)和服务类型(HTTP、FTP)等。  
	- 当数据包与规则匹配时，iptables就根据规则所定义的方法来处理这些数据包，比如放行(ACCEPT)、拒绝(REJECT)、 丢弃(DROP)等
4. 主机处理报文流程
![[1144139-20200831111054990-1558810125.png]]
5. `iptables` 表和链: 
	 链的位置可以修改报文 , 检查是否允许放行等 , 链包括`PREROUTING` (路由前) , `FORWARD` (转发) , `POSTROUTING` (路由后)
	- `filter` iptables默认表 , 负责过滤 , 有三种内建链(chains)
		- `INPUT` 链 , 负责处理来自外部的数据
		- `OUTPUT` 链接 , 处理向外发送的数据
		- `FORWARD` 将数据转发到本机的其他网卡设备上
	- `nat` 负责网络地址转换 , 他又三个链
		- `PREROUTING` 链 , 处理刚到达本机并在路由转发前的数据包。它会转换数据包中的目标IP地址（destination ip address），通常用于DNAT(destination NAT)。
		- `POSTROUTING` 链 , 处理即将离开本机的数据包。它会转换数据包中的源IP地址（source ip address），通常用于SNAT（source NAT）
		- `OUTPUT` 处理本机产生的数据包
	- `mangle` 用于指定如何处理数据包。它能改变TCP头中的QoS位。Mangle表具有5个内建链（chains）
		- `PREROUTING`
		- `OUTPUT`
		- `FORWARD`
		- `INPUT`
		- `POSTROUTING`
	- `raw` 用户处理异常 , 他有两个内建链
		- `PREROUTING chain`
		- `OUTPUT chain`
	- `security`
	
6. `netﬁlter`处理过程:
	内核会按照顺序依次检查 iptables 防火墙规则，如果发现有匹配的规则目录，则立刻执行相关动作，停止继续向下查找规则目录；如果所有的防火墙规则都未能匹配成功，则按照默认策略处理。使用 `-A `选项添加防火墙规则会将该规则追加到整个链的最后，而使用 `-I` 选项添加的防火墙规则则会默认插入到链中作为第一条规则
#### 2. 使用
1. 格式 `iptables [-t table] COMMAND chain CRETIRIA -j ACTION` 
	- `-t table` 指定表格  , 默认为`filter`表
	- `COMMAND` 定义如何对规则进行管理
	- `chain` 指定链 , 当定义策略的时候，是可以省略
	- `CRETIRIA` 匹配参数
	- `-j ACTION` 触发动作

2. 常用选项
	- `-A`	添加防火墙规则
	- `-D`	删除防火墙规则
	- `-I`	插入防火墙规则
	- `-F`	清空防火墙规则
	- `-L`	列出添加防火墙规则
	- `-R`	替换防火墙规则
	- `-Z`	清空防火墙数据表统计信息
	- `-P`	设置链默认规则
3. 命令常用匹配参数
	- `[!]-p` 匹配协议，! 表示取反
	- `[!]-s` 匹配源地址
	- `[!]-d` 匹配目标地址
	- `[!]-i` 匹配入站网卡接口
	- `[!]-o`匹配出站网卡接口
	- `[!]--sport`匹配源端口
	- `[!]--dport`	匹配目标端口
	- `[!]--src-range`	匹配源地址范围
	- `[!]--dst-range`	匹配目标地址范围
	- `[!]--limit`	四配数据表速率
	- `[!]--mac-source`	匹配源MAC地址
	- `[!]--sports`	匹配源端口
	- `[!]--dports`	匹配目标端口
	- `[!]--stste`	匹配状态（INVALID、ESTABLISHED、NEW、RELATED)
	- `[!]--string`	匹配应用层字串
4. 常用触发动作
	- `ACCEPT`  允许数据包通过
	- `DROP`  丢弃数据包
	- `REJECT`   拒绝数据包通过
	- `LOG`   将数据包信息记录 syslog 曰志
	- `DNAT`  目标地址转换
	- `SNAT`  源地址转换
	- `MASQUERADE`  地址欺骗
	- `REDIRECT`   重定向
5. 例: 
```bash
## 1.添加允许入站 SSH 流量(端口22)
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

## 2.阻止入站ping请求
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

## 3.端口转发
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination NEW_IP:NEW_PORT

## 4.如果来自同一源 IP 地址的 TCP 连接尝试连接到目标端口 80 的数量超过 10 个，那么拒绝这些连接并向发送端发送拒绝消息
iptables -A INPUT -p tcp -m tcp --dport 80 -m connlimit --connlimit-above 10 -j REJECT

## 5.只允许特定主机/网段的 SSH 流量(端口22)
iptables -A INPUT -s 10.10.10.3 -p tcp --dport 22 -j ACCEPT 
iptables -A INPUT -s 10.10.10.0/24 -p tcp --dport 22 -j ACCEPT

## 6.只允许10.10.10.3之外的主机链接
iptables -A INPUT -s ! 10.10.10.3 -p tcp --dport 22 -j ACCEPT

## 7.允许从 `eth0` 到 `eth1` 的已建立连接或相关连接的数据包通过。
iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
## 8.允许从 `eth1` 到 `eth0` 的所有数据包通过，而不考虑连接的状态
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT

## 规则7,8这两条规则共同工作以允许已建立的连接和相关连接从 `eth0` 到 `eth1`，同时允许所有的数据包从 `eth1` 到 `eth0`，形成了一个简单的基本的双向转发规则。这种设置通常用于充当网络中继或网关的设备 , 用于开启数据包转发功能

# 9.处理IP碎片数量，防止DDOS攻击，允许每秒100个
iptables -A FORWARD -f -m limit --limit 100/s --limit-burst 100 -j ACCEPT

# 10.本机端口重定向
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-ports 8080 
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j REDIRECT --to-ports 8443

iptables -P INPUT DROP 
##丢弃所有关于外界连接 , 所有规则包括Xshell连接之类的
iptables -t nat -F
##清空nat表所有链
iptables -L -n -v --line-numbers
##在规则前添加行号 , 并显示规则详情
```