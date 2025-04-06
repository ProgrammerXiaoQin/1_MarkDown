##### 1.简介
1. OSPF（Open Shortest Path First）开放式最短路径优先，IETF定义的一种基于链路状态的内部网关路由协议，属于链路状态路由协议。适用于中大型企业网络、数据中心等需要高效动态路由的环境（OSPF默认优先级为10，数值越小优先级越高，hello间隔默认10s，死亡间隔默认40s）
2. 特点：
	1. 无环路
	2. 收敛快
	3. 扩展性好
	4. 支持认证

3. OSPF报文
	1. OSPF报文封装在IP报文中，协议号为89。
	2. OSPF报文类型：
		1.  Hello报文：于发现邻居、维持邻居关系，周期性发送（默认10秒/30秒）
		2. DD（Database Description）报文：数据库描述报文，交换链路状态数据库的摘要信息。
		3. LSR （Link State Request）报文：链路状态请求报文，请求缺失的LSA详细信息
		4. LSU（Link State Update）报文：链路状态更新报文），携带具体的LSA内容（如拓扑变化）
		5. LSACL（Link State Acknowledgement）报文：链路状态确认报文，确保LSA可靠传输

4. OSPF七种状态：
	1. Down：初始状态，未收到任何Hello报文。
	2. Init：收到邻居的Hello报文，但对方的邻居列表中未包含本路由器的Router ID。
	3. 2-Way：双方Hello报文中均包含对方的Router ID，邻居关系建立（但未同步数据库）。
	4. ExStart：协商主从（Master/Slave）关系，确定DBD报文的序列号以同步数据库。
	5. Exchange：交换DBD报文，互相发送链路状态数据库的摘要信息。
	6. Loading：比较数据库差异，通过LSR/LSU报文请求并获取缺失的LSA。
	7. Full：邻接关系完全建立，双方数据库完全同步。

5. OSPF开销计算公式：`开销（cost）= 参考带宽(默认100Mb)/实际带宽`
	1. 不满1按照1算
##### 2. 配置
1. OSPF 单区域配置：
```
命令：ospf <Process ID> router-id <IP_ADDR>
	1. Process ID ：ospf进程id，通常设置为1
	2. IP_ADDR：OSPF专用路由器ID值（IPv4地址格式），一般设为：1.1.1.1，2.2.2.2，3.3.3.3。。。。

# 进入OSPF配置
# process-id 是本地进程ID（默认为1）
[Router] ospf [process-id]

# 指定Router ID
# 如果未手动配置，路由器会自动选择最大的接口IP地址作为Router ID。
[Router-ospf-100] router-id x.x.x.x

# 创建区域并宣告网段
# area-id 是区域号（骨干区域为0）
[Router-ospf-100] area area-id 
[Router-ospf-100-area-0.0.0.0] network ip-address wildcard-mask

# 接口下启用OSPF（替代network命令）
# [Router] interface G0/0/0
# [Router-G0/0/0] ospf enable process-id area area-id

注：其他区域，入area 1，area 2，必须与骨干区域相邻
```

2. OSPF多区域配置
>注：划分好路由器区域后，注意连接两个不同区域之间的路由器的不同接口应该属于不同区域

3. OSPF可选配置
	1. 区域认证
		1. 简单明文密码： `[R-ospf-1-area-0] authentication-mode simple plain Password123`
		2. md5加密：`[R-ospf-1-area-0] authentication-mode md5 1 cipher Password123`
	2. 接口认证： `[R-G0/1]ospf authentication-mode md5 1 cipher Password123`
	3. 调整OSPF DR选举优先级：`[R-G0/1] ospf dr-priority number` 
		- （默认为1，范围0-255，越大优先级越高，优先级相同router-id越大优先级越高，0不参与选举）
	4. 修改接口开销（Cost）：`[R-G0/1]ospf cost 100`（只能修改入方向)
		1. 修改时链路两端接口的Cost调整一致，避免单向流量问题
	5. 设置端口为被动的端口（不会主动发hello包，一般连接客户端的接口需要设置）：`[R4-ospf-1] silent-interface G0/0/1`
	6. 修改参考带宽：`[R4-ospf-1]bandwidth-reference [number]` （单位MB，默认100）
	7. 修改OSPF优先级：`[R4-ospf-1]preference [number]` （默认为10）

##### 3.相关命令
1. 查看OSPF邻居状态：`display ospf peer [brief]`
2. 查看OSPF数据库 ：`display ospf lsdb `
3. 查看OSPF路由表：`display ip routing-table protocol ospf`
4. 重新设置OSPF路由ID：
	1. `ospf <Process ID> router-id <NEW_IP_ADDR>`
	2. 切换用户视图，`reset ospf process`
5. 查看OSPF DR状态：`display ospf interface`