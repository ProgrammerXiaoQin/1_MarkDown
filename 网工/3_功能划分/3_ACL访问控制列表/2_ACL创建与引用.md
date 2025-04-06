>注：在 基本 ACL和 高级 ACL 中，IP 地址的匹配使用通配符掩码（Wildcard Mask），与子网掩码（Subnet Mask）逻辑相反

##### 1. 基本配置acl （2000-2999）：
1. 语法：`rule [rule-id] [permit/deny] source <ip-address> <wildcard-mask>` 

```
# 创建基本 ACL 2000
acl  2000  

# 拒绝 192.168.1.0/24 网段
rule 5 deny source 192.168.1.0 0.0.0.255  

# 允许其他所有 IP 的流量
rule 10 permit source any
```

##### 2. 高级ACL配置（3000-3999）
1. 语法：`rule [rule-id] [permit/deny] <protocol> source <ip> <mask> destination <ip> <mask> [port-operators]`

```
# 允许来自 192.168.1.0/24 网段的所有 IP 流量通过，其他流量默认拒绝
（默认隐含，但显式配置更清晰）
acl 3000  
 rule 5 permit ip source 192.168.1.0 0.0.0.255   
 rule 10 deny ip        

# 允许外部访问10.0.0.1的80和443端口，其他端口拒绝
acl 3000  
 rule 5 permit tcp destination 10.0.0.1 0 destination-port eq 80  
 rule 10 permit tcp destination 10.0.0.1 0 destination-port eq 443  
 rule 15 deny ip     

# 允许网络设备被 Ping（用于连通性测试），但拒绝其他所有流量
acl 3000  
 rule 5 permit icmp   
 rule 10 deny ip    

# 仅允许管理员 IP `172.16.1.100` 通过 `SSH（端口22）` 登录交换机
acl 3000  
 rule 5 permit tcp source 172.16.1.100 0 destination-port eq 22  
 rule 10 deny tcp destination-port eq 22                         
 rule 15 permit ip     
  
```

##### 3. 二层ACL（4000-4900）
```
# 语法：
rule [rule-id] [permit/deny] [l2-field] <value>

# 拒绝特定 MAC 地址
rule 5 deny source-mac 00e0-fc01-0101 ffff-ffff-ffff  

# 允许优先级为 3 的 VLAN 流量
rule 10 permit 8021p 3                                
```

##### 4.ACL引用
```
# 创建ACL规则2000
[RTA]acl 2000
[RTA-acl-basic-2000]rule deny source 192.168.1.0 0.0.0.255

# 在接口上指定流量过滤，出设备方向，使用ACL2000
[RTA]interface Gigabit0/0/0
[RTA-G-0]traffic-filter outbound acl 2000
```
- `traffic-filter { inbound | outbound } acl [acl-number]` 
	- inbound : 入设备方向
	- outbound：出设备方向

##### 5.相关命令
1. `display traffic-filter applied-record` 查看基于ACL的报文过滤的应用信息
2. `display acl {acl-number| all}` 查看ACL信息