##### 1.基于接口划分
```
# 配置接口Eth0/0/3为access类型，并划分给vlan20
[SW1]interface Eth0/0/3
[SW1-Ethernet0/0/3]port link-type access #配置接口为access类型
[SW1-Ethernet0/0/3]port default vlan 20  #配置缺省VLAN 20并加入

# 配置接口G0/0/1为trunk类型，并划分给vlan10，vlan20
[SW2]vlan batch 10 20                    #创建vlan10和vlan20
[SW2]interface G0/0/1
[SW2-G0/0/1]port link-type trunk  #配置接口为trunk类型
[SW1-G0/0/1]port trunk allow-pass vlan 10 20 

# 配置VLAN 10的描述（可选）
[SW3] vlan 10
[SW3-vlan10] description Department_A
[SW3-vlan10] quit

# 同时配置多个端口
[Huawei] vlan batch 10 20
[Huawei] port-group group1              # 创建端口组（批量配置）
[Huawei-port-group-group1] group-member GigabitEthernet 0/0/1 to GigabitEthernet 0/0/5
[Huawei-port-group-group1] port link-type access
[Huawei-port-group-group1] port default vlan 10
[Huawei-port-group-group1] quit
```

##### 2. MAC地址应用VLAN
```
# 创建vlan并绑定MAC地址
[SW3]vlan batch 10 20
[SW3]vlan 10 
[SW3-vlan10]mac-vlan mac-address 0000-0000-00A1 #绑定mac地址
[SW3-vlan10]mac-vlan mac-address 0000-0000-00A2 #绑定mac地址
[SW3-vlan10]quit

[SW3]vlan 20 
[SW3-vlan10]mac-vlan mac-address 0000-0000-00B1 #绑定mac地址
[SW3-vlan10]mac-vlan mac-address 0000-0000-00B2 #绑定mac地址
[SW3-vlan10]quit

# 配置基于MAC地址绑定vlan
[SW3]interface G0/0/1
[SW3-G0/0/1]mac-vlan enable  #开启接口基于mac地址划分vlan功能
[SW3-G0/0/1]port link-type hybrid  #设置接口类型为hybrid
[SW3-G0/0/1]port hybrid untagged vlan 10 20 #对vlan10,20的报文剥离vlan tag
[SW3-G0/0/1]quit
```

此时交换机口G0/0/1：
1. 接收：将源MAC A1和A2的的流量划入VLAN 10，B1和B2的的流量划入VLAN 20,其他MAC划进PVID（默认为1）
2. 转发：转发时带上vlan tag 
3. 发送（发送给主机）：剥离vlan tag 标签

相关命令
1. `display mac-vlan vlan <vlan-id>`  查看MAC-VLAN绑定表
2. `port hybrid pvid vlan <vlan-id>`  设置hybrid PVID

##### 3.基于子网划分vlan
```
# 创建VLAN 10和20
<Huawei> system-view
[Huawei] vlan batch 10 20

# 配置子网与VLAN的绑定关系
[Huawei] vlan 10
[Huawei-vlan10] ip-subnet-vlan 192.168.10.0 24  # 绑定子网到VLAN 10
[Huawei-vlan10] quit

[Huawei] vlan 20
[Huawei-vlan20] ip-subnet-vlan 192.168.20.0 24  # 绑定子网到VLAN 20
[Huawei-vlan20] quit

# 配置接口启用基于子网的VLAN功能
[Huawei] interface GigabitEthernet 0/0/1
[Huawei-GigabitEthernet0/0/1] port link-type hybrid  # 需为Hybrid或Trunk模式
[Huawei-GigabitEthernet0/0/1] ip-subnet-vlan enable  # 启用基于子网的VLAN
[Huawei-GigabitEthernet0/0/1] port hybrid untagged vlan 10 20  # 发送时剥离标签（可选）
[Huawei-GigabitEthernet0/0/1] port hybrid pvid vlan 1         # 默认VLAN设为1（未匹配时生效）
[Huawei-GigabitEthernet0/0/1] quit

# 保存配置
[Huawei] save
```