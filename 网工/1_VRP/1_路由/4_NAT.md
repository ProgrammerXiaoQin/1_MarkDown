#### 一. 简介
1. 网络地址转换技术NAT(Network Address Translation)，主要用于实现内部网络的主机访问外部网络的功能。当局域网内主机需要访问外部网络，通过NAT技术可以将私网地址转换为公网地址。且多个私网用户可以共用一个公网地址。
	- 实现内外网络互通
	- 节省公网地址
	- NAT一般部署在连接内外网的网关设备上

2. 实现方式
	- 静态NAT：私有地址和公有地址一对一映射
	- 动态NAT：基于地址池实现私有地址和公有地址转换
	- NAPT：网络地址端口转换，允许多个内部地址映射到同一个公有地址的不同端口
	- Easy IP：允许多个内部地址映射到网关出接口地址上的不同端口

#### 二. 配置
1. 静态NAT : 
```
# 1.配置静态NAT映射
[R1-G0]nat static global [公网地址] inside [私网地址]
# 2.启用静态NAT
[R1-G0]nat static enable
```

2. 动态NAT/NAPT：
```H3C
### 内网IP：192.168.1.1，公网IP：202.100.1.1 

## 1.匹配内网流量
acl basic 2000
rule permit source 192.168.1.0 0.0.0.255  # 允许转换的内网网段

## 2.设置NAT地址池
nat address-group 1
address 202.100.1.2 202.100.1.5  # 公网IP地址池

## 3.外网接口启动NAT
[G0/1]nat outbound 2000 [address-group 1]  # 不写address-group则默认使用接口IP（PAT）
```



#### 三. 其他相关命令
1. 查看静态NAT配置信息：`[R]display nat static `