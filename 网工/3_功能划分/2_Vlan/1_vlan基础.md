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