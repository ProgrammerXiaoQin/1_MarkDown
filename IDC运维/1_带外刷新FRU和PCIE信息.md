1. 准备ipmitool工具
	- windows直接使用免安装软件，Linux可直接使用包管理器下载

2. 刷新FRU
```cmd
###查看带外FRU信息
ipmitool -I lanplus -H [bmc ip] -U [user] -P [password] fru list 0

###刷新Product Manufacturer（厂家）
ipmitool fru edit 0 field p 0 '***'

###刷新Product Name（型号）
ipmitool fru edit 0 field p 1 '***'

###刷新Product Part Number（产品型号）
ipmitool fru edit 0 field p 2 '***'

###刷新Product Version（序列号）
ipmitool fru edit 0 field p 3 '***'

###刷新Product Serial（固资号）
ipmitool fru edit 0 field p 4 '***'

###刷新Product Asset Tag（资产编号）
ipmitool fru edit 0 field p 5 '***'

###刷新Product Extra（套餐）
ipmitool fru edit 0 field p 7 '***'

###刷新完power cycle生效
ipmitool power cycle
```

3. 刷新PCIE拓扑
	- PCIE拓扑是与套餐唯一对应的,其以bin文件方式烧录
	- PCIE烧录工具和拓扑下载后必须放在IPMIToolWin这个工具目录下

```cmd
###带外烧录,其中suite1.bin即对应拓扑bin文件
python3 PcieEEpromTool.py -H [ip] -U [usr] -P [pwd] -I lanplus -W suite1.bin

###带外校验,其中suite1.bin即对应拓扑bin文件
python3 PcieEEpromTool.py -H [ip] -U [usr] -P [pwd] -I lanplus -C suite1.bin

```

4. 更新固件，不保存配置

5. 配置BMC ipv6地址，掩码64位，网关:BMC ipv6地址最后一位改为1

6. 其他相关命令
```
###清除相关SEL
ipmitool sel clear

###重启后检查pcie拓扑是否有报错
ipmitool sel elist | grep -i bacc

###PSU配置 配置2或4psu方法，需要重启BMC生效
ipmitool raw 0x3e 0x2d 0x00 0x02/0x04
ipmitool mc reset cold

###查询
ipmitool raw 0x3e 0x2e 0x00
04 02/04 //第一个04代表满配置4个PSU,第二个02时则表示当前配置是2个PSU,04时则表示当前配置是4个PSU

```

7. IPMI命令配置用户
1. 查询当前 BMC 用户列表（确认可用 ID）
	- `ipmitool user list 1` （ 1为默认LAN通道号，部分机型为2优先选择空的ID创建新用户。）

2. 创建新用户（指定 ID 和用户名）
	- `ipmitool user set name 2 testuser `（ 2=用户ID，testuser=自定义用户名）

3. 设置用户密码
	- `ipmitool user set password 2 Test@12345` （ 2=用户ID，Test@12345=密码 需符合复杂度，特殊字符要加单引号）

4. 启用用户的 IPMI 权限
    - `ipmitool user enable 2` （ 2=用户ID 启用后才能接收IPMI指令）

5. 配置用户权限等级（关键）
	- `ipmitool channel setaccess 1 2 link=on ipmi=on callin=on privilege=4`（配置2号用户在1号通道的权限为ADMINISTRATOR）
		- `channel 1`：网络通道
		- `2`：用户 UID
		- `privilege=4`：最高权限

6、查看添加用户是否添加成功，权限是否生效

ipmitool user list 1