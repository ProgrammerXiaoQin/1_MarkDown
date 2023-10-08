1. 简介
	- LVM(Logical volume Manager) 逻辑卷管理系统
	- 通过将底层物理硬盘抽象封装起来 , 以逻辑卷的方式表现给上层系统
	- 逻辑卷大小可以动态调整 , 且不会丢失现有数据 , 新加入的硬盘也会改变现有上层逻辑卷

2. 原理
	1. 物理磁盘格式化为PV , 空间被分配成一个个PE
	2. 不同的PV可以加入同一个VG 
	3. LV基于PE创建 , 大小为PE的整数倍 , 组成LV的PE可能来自不同的物理磁盘
	4. LV可以格式化后就可以直接挂使用 , 对系统来说一个LV就是一个分区
	5. LV的增减实际上就是增减组成该LV的PE数量 , 其过程中不丢失原始数据
	6. 构成 :   
		- PE(physical Extend) , 物理扩展 ,LVM空间管理最基本单位 , 默认4Mb大小
		- PV(physical Volume) , 物理卷 , 个可供存储LVM的块设备. 例如: 一块硬盘, 一个分区, 一个回环文件, 一个被内核映射的设备 , 它包含一个特殊的LVM头
		- VG(volume group) , 卷组(空间池) , 可以把一个或多个PV加入一个VG中
		- LV(logical volume) , 逻辑卷
		

3. 创建LVM
	1. 将物理磁盘设备初始化为物理卷PV
		- `pvcreate [硬盘]` 
		- -f：强制创建物理卷，不需要用户确认
		- -u：指定设备的UUID
		- -y：所有的问题都回答“yes”
		- -Z：是否利用前4个扇区
	2. `vgcreate [name] [硬盘]`  创建卷组 , 并将PV加入卷组中
	3. 基于卷组创建逻辑卷
		- `lvcreate -n [LVname] -L 2G [VGname]`
		- -n 指定逻辑卷名
		- -L 指定逻辑卷大小(容量)
		- -l 指定逻辑卷大小(PE个数) , 可用`+100%FREE` ,表示将所有可用空间都添加到逻辑卷LV
	4. 为逻辑卷创建文件系统 , 格式化后可直接使用
		- `mkfs[tab][tab] [lv硬盘]`
		- lv设备一般存在 `/dev/vgname/lvname`(软连接) , 实际位置在`/dev/mapper/`下

4. 拉伸LV , 逻辑卷 , 拉伸操作可以在线执行, 不需要卸载逻辑卷
	1. `vgdisplay` 保证VG中有足够的空闲空间
	2. 扩充逻辑卷
		- `lvextend -L +2G [/dev/vgname/lvname]`
		- -L 指定逻辑卷大小(容量)
		- -l 指定逻辑卷大小(PE个数) , 可用`+100%FREE` ,表示将所有可用空间都添加到逻辑卷L
	3. `resize2fs /dev/vgname/lvname` 更新文件系统

5. 拉伸VG , 卷组
	1. 将物理硬盘设备初始化为物理卷PV
		- `pvcreate [硬盘]`
	2. 将PV , 物理卷添加到VG , 卷组
		- `vgextend [vgname] [硬盘]`

6. 缩小逻辑卷 , 缩小逻辑卷必须离线执行
	1. `umount /dev/vgname/lvname` 卸载已经挂载的逻辑卷
	2. `resize2fs /dev/vgname/lvname 10G` 将文件系统缩小为`10G`
	3. `lvreduce -L -1G /dev/vgname/lvname` , 缩小1G , LV空间
	4. 注 : 当缩小的逻辑卷空间和减掉空间的后LV大小不一致的时候可能会损坏数据
	5. 重新挂载
	
7. 缩小卷组
	1. `vgreduce vgname /dev/sdc` 将PV从指定VG中移除
	2. 注 : 移除时要保证VG空闲空间比要移除的PV大

9. 其他命令
	1. **pvs** 查看现有pv , **pvdisplay**查看pv详细信息
	2. **vgs** 查看现有vg , **vgdisplay**查看pv详细信息
	3. **lvs** 查看现有vg , **lvdisplay**查看pv详细信息
	4. **lvremove**  , `lvremove /dev/vgname/lvname`删除逻辑卷
	5. **vgremove** , `vgremove lvname` 删除卷组
	6. **pvremove** , `pvremove pvname` 删除物理卷
	7. **lvmdiskscan** , 列出可使用的物理卷设备