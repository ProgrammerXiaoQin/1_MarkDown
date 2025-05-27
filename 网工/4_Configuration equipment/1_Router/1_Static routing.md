1. 命令格式 : `ip route-static <目标网段> <掩码> <下一跳地址或出接口>`
	- 目标网络和掩码如果是全0 `0.0.0.0 0` ,表示默认路由
e.g:
```
ip route-static 0.0.0.0 0 10.1.1.254  # 所有流量默认发送给网关10.1.1.254

ip route-static 0.0.0.0 0 GigabitEthernet0/1  # 通过指定出接口发送（直连场景）
```

2. 浮动路由
```
ip route-static 0.0.0.0 0 10.1.1.254 preference 70  # 默认优先级60，设置70作为备份
```

3. 其他相关命令
	1. `display ip routing-table` 查看路由表
	2. `undo ip route-static <目标网段> <掩码> <下一跳或接口>` 删除静态路由条目