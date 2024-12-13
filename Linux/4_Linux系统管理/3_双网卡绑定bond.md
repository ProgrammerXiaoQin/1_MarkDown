#### 1. 简介
1. 什么是bond：网卡bond是通过多张网卡绑定未一个逻辑网卡，实现本地网卡冗余，带宽扩容和负载均衡

2. bond常用模式：
	1. mode=0（balance-rr），表示负载分担round-robin，使用轮询的方式比如第一个包走eth0，第二个包走eth1，直到数据包发送完毕。
		 - 优点：流量提高一倍
		 - 缺点：需要接入交换机做端口聚合，否则无法使用
	2. mode=1（active-backup），表示主备模式，即同时只有一块网卡在工作
		- 优点：冗余性高
		- 缺点：链路利用率低，两块网卡只有1块在工作
	3. mode=4，表示802.3ad 动态链路聚合模式，这种模式允许两个或多个物理以太网接口聚合在一起，作为一个逻辑接口使用，从而提高带宽并提供冗余
		1. 注意事项：
			- **兼容性**：确保两端设备都支持 802.3ad 标准。
			- **配置一致性**：两端的聚合配置必须一致，包括 LACP 的速率等。
			- **负载均衡策略**：根据实际需求选择合适的负载均衡策略。

#### 2. 操作
```bash
1.nmcli connection add type bond ifname bond0 mode 0
#创建一个bond连接，设备名bond0，采用mode 0模式
#会在/etc/sysconfig/network-scripts/下生成一个ifcfg-bond-bond0文件

2.nmcli connection add type bond-slave ifname ens37 master bond0
#添加一块隶属卡，将ens37网卡设置为bond0的从卡
#会在/etc/sysconfig/network-scripts/下生成一个ifcfg-bond-slave-ens37文件
3.nmcli connection add type bond-slave ifname ens38 master bond0
#添加一块隶属卡，将ens38网卡设置为bond0的从卡

4.修改/etc/sysconfig/network-scripts/ifcfg-bond-bond0文件，配置IP地址

5.重启network服务，如果重启失败可以尝试停止NetworkManager.service

6.查看bond0状态 cat /proc/net/bonding/bond0
```

#### 3. 配置文件
```bash
#ifcfg-bond-bond0

BONDING_OPTS=mode=balance-rr
#bond工作模式
TYPE=Bond
BONDING_MASTER=yes
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
#如何获取IP地址，dhcp为自动获取，建议改为none或static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=bond-bond0
UUID=e2b5045c-975c-4fc8-8eb6-357d8f3de1b3
DEVICE=bond0
ONBOOT=yes
IPADDR=192.168.10.240
#IP地址
NETMASK=255.255.255.0
#掩码
GATEWAY=192.168.10.254
#网关
DNS1=8.8.8.8
DNS2=199.29.29.29
#DNS地址
```
