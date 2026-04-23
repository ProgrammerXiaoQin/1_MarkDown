1. 运维平台报错`Power_Fault` 
2. 到现场发现服务器关机状态，判断为`Power_Fault`导致机器关机，在不下电状态下收集日志
	- 下电后收集日志可能会丢失关键信息
3. 分析日志，发现`Power_Fault` 是由`PVPP_CPU0_GROUP1_FAULT` 引起的，通过文档[浪潮服务器Power_Fault指向说明](https://admin-property.feishu.cn/file/TaLAbY5GHo5AOYxfEU7cYJbqnSf) 判断可能是主板或内存引起的
	- 日志路径`dump_xxxx\onekeylog\log\idl.log` 
	- 可能问题内存`p0_c4_d0,p0_c4_d1---p0_c7_d1`
4. 下架服务器，发现服务器只差了16根内存（p0_c0_d0--p1_c7_d0,满配32,），拔下可能有问题内存ABCD中CD，尝试开机，可以开机，关机插入C，发现可以开机，判断为内存D坏了
	- 如果可以直接开机，需要交换两个cpu所有可能会坏的内存，多尝试几次，如果还可以开机，可以判断为误报
	- 如果可能问题内存组插入正常内存无法开机，判断为主板问题
