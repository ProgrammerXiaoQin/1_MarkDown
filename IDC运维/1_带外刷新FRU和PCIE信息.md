1. 准备ipmitool工具
	- windows直接使用免安装软件，Linux可直接使用包管理器下载

2. 刷新FRU
```cmd
###查看带外FRU信息
ipmitool -I lanplus -H [bmc ip] -U [user] -P [password] fru list 0

###刷新Product Manufacturer（厂家）
ipmi fru edit 0 field p 0 '***'

###刷新Product Name（型号）
ipmi fru edit 0 field p 1 '***'

###刷新Product Part Number（产品型号）
ipmi fru edit 0 field p 2 '***'

###刷新Product Version（序列号）
ipmi fru edit 0 field p 3 '***'

###刷新Product Serial（固资号）
ipmi fru edit 0 field p 4 '***'

###刷新Product Asset Tag（资产编号）
ipmi fru edit 0 field p 5 '***'

###刷新Product Extra（套餐）
ipmi fru edit 0 field p 7 '***'

###刷新完power cycle生效
ipmi power cycle
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

4. 结束
	- 清除事件日志，并断电重启生效


5. 其他相关命令
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