##### 1. VLAN基本概念
VLAN(Virtual Local Area Network）即虚拟局域网，是将一个物理的LAN在逻辑上划分成多个广播域的通信技术。归属同一VLAN的主机间可以直接通信，而归属不同VLAN的主机间不能直接互通，从而实现将广播报文限制在一个VLAN内部。在一个 VLAN交换网络中，以太网帧有以下两种形式:
- 有标记帧（tagged frame)：加入了4字节802.1Q标记的帧。
- 无标记帧（untagged frame)：原始的、未加入4字节802.1Q标记的帧。

##### 2.链路类型
VLAN中有以下两种链路类型：
1. 接入链路（Access Link)：用于连接用户主机和交换机的链路。通常情况下，主机并不需要知道自己属于哪个 VLAN，主机硬件通常也不能识别带有VLAN标记的帧。因此主机发送和接收的帧是不带标记帧。
2. 干道链路（Trunk Link）：通常用于交换机间的连接。干道链路可以承载多个不同 VLAN数据，数据帧在干道链路传输时，干道链路的两端设备需要能够识别数据帧属于哪个 VLAN，所以在干道链路上传输的帧通常是带标记帧。

##### 3. 常用命令
1. `display vlan` 查看交换机当前VLAN信息
2. `display port vlan` 查看交换机各接口所属VLAN信息
3. `vlan [number]` 创建vlan number
	- `vlan batch 11 12` 同时创建vlan 11和vlan 12

##### 3. 创建并将接口加入VLAN
```
[SW1]interface Eth0/0/3
[SW1-Ethernet0/0/3]port link-type access #配置接口为access类型
[SW1-Ethernet0/0/3]port default vlan 20 #配置缺省VLAN 20并加入

[SW2]interface G0/0/1
[SW2-G0/0/1]port link-type trunk  #配置接口为trunk类型
[SW1-G0/0/1]port trunk allow-pass vlan 10 20 
```
1. `port link-type` ： 配置接口的链路类型
	- 缺省情况下，接口的链路类型为Hybrid。
2. `port default vlan 20` ：配置接口的缺省VLAN并同时加入这个VLAN
3. `port trunk allow-pass vlan [number]` ：配置Trunk类型虚拟接口加入的VLAN并设置允许加入的VLAN通过

##### 4. 基于MAC地址应用VLAN
```
[SW3]vlan batch 10 20
[SW3]vlan 10
[SW3-vlan10]mac-vlan mac-address 0000-0000-00A1 #绑定mac地址
[SW3-vlan10]quit

[SW3]interface G0/0/1
[SW3-G0/0/1]mac-vlan enable  #开启接口基于mac地址划分vlan功能
[SW3-G0/0/1]port link-type hybrid  #设置接口类型为hybrid
[SW3-G0/0/1]port hybrid untagged vlan 10 20 #允许vlan 10 20通过，且帧发送时去掉vlan标记
[SW3-G0/0/1]quit
```
配置后，通过G1接口（如其他路由器，主机传过来）的流量会按照mac-vlan规则，给帧带上对应vlan tag。并且从该接口发送出去的帧则去表vlan标记

1. `mac-vlan mac-address 0000-0000-00A1` ：配置MAC地址与VLAN关联
	- 若某VLAN配置为MAC VLAN，必须先使用**undo mac-vlan mac-address**命令删除所有MAC地址与VLAN的关联后，才能删除该VLAN。
	- 一个MAC地址关联了MAC VLAN后，则不可以再用于配置其它MAC VLAN。
2. `mac-vlan enable` ：开启接口基于mac地址划分vlan功能
3. `port hybrid untagged vlan 10 20` ：配置Hybrid类型接口加入的VLAN，这些VLAN的帧以Untagged方式通过接口


##### 5. VLAN间相互通信
```
1. 创建vlan 10，20，将G1设置为trunk口并允许vlan 10，20通过，G2依旧

[RS1]interface Vlanif 10 #进入vlanif10接口
[RS1-Vlanif10]ip address 192.168.64.254 24 #设置该SVI接口IP和掩码
[RS1-Vlanif10]quit

[RS1]interface Vlanif 20
[RS1-Vlanif10]ip address 192.168.64.254 24
[RS1-Vlanif10]quit
```
此时在三层路由器RS1上可以通过`display ip routing-table`可以查看到路由表中添加了这两SVI相关的路由条目，此时通过G1，G2接口的VLAN10,20流量可以和相互通信
>注：`display ip interface brief` 可以看到VLANif简要信息，`display interface vlanif [vlan number]` 查看详细信息

1. `interface Vlanif 10` ：进入vlanif10接口
2. `ip address 192.168.64.254 24` ：设置对应接口的IP和掩码