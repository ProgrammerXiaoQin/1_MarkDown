#### 1. 配置三层接口DHCP
```
<HUAWEI> system-view
[HUAWEI] dhcp enable
[HUAWEI] interface vlanif 100
[HUAWEI-Vlanif100] ip address 10.1.1.2 24
[HUAWEI-Vlanif100] dhcp server dns-list 8.8.8.8 # 可选配置DNS
[HUAWEI-Vlanif100] dhcp select interface
```
此时，直连此接口的设备可以自动获取IP地址

#### 2. 配置DHCP地址池     
```
<HUAWEI> system-view
[HUAWEI] dhcp enable
[HUAWEI] ip pool POOL1
# 创建DHCP地址池,取名为POOL1
.....
[HUAWEI] interface GigabitEthernet0/0/1
[HUAWEI-G-0]dhcp select global
[HUAWEI-G-0]ip address 192.168.2.1 24
# 配置接口IP，确保接口IP属于地址池网段
```
1. DHCP地址池常用参数配置
	1. `network 192.168.1.0 mask 255.255.255.0` 分配网段和掩码
	2. `gateway-list 192.168.1.1` 设置默认网关 （通常为路由器接口IP）
	3. `dns-list 114.114.114.114 8.8.8.8` 指定DNS服务
	4. `lease day 3 hour 0 minute 0` （可选）设置租期时间，默认1天
	5. `excluded-ip-address 192.168.1.1 192.168.1.10` （可选）排除保留IP ，如保留`192.168.1.1`到`192.168.1.10` 用于静态分配

#### 3.相关命令
1. `display ip pool name POOL1` 查看地址池POOL1分配情况
2. `display dhcp server statistics` 检查DHCP服务状态

#### 4. 配置DHCP中继
1. 当DHCP服务器和主机不在一个网段时需要配置DHCP中继，如R1 G1属于1网段，G2属于2网段，连接R2，DHCP服务在R2上

```
##### R1 #####
<HUAWEI> system-view
[HUAWEI] interface G/1
[HUAWEI-G0/1] dhcp enable
[HUAWEI-G0/1] ip address 10.1.1.2 24
[HUAWEI-G0/1] dhcp select relay
# dhcp选择为中继模式
[HUAWEI-G0/1] dhcp relay server-ip 192.168.2.1
# 设置DHCP服务器IP

##### R2 #####
对应接口开启DHCP服务
```

此时，R1 G1下的子网可以接收到R2的DHCP分配信息

#### 5. DHCP Snooping
```
[SW]dhcp enable
[SW]snooping enable

# 给所有端口开启DHCP snooping
[SW]port-group group-member G0/0/1 to G0/0/24
[SW-port-group]dhcp snooping enable 

# 单独进入G1口，开启DHCP信任
[SW]interface G0/0/01
[SW]dhcp snooping trusted 

# 此时交换机下设备只能从连接G0/0/01的设备获取DHCP的信息
```