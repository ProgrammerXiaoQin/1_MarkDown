1. 简介：kysec是银河麒麟下一款安全管理工具
2. 示例
```
# 查看当前 KySec 状态
sudo getstatus
# 启用 KySec 并设置为 Normal 模式（强制模式）
sudo setstatus enable
sudo setstatus Normal
# 重启系统以生效
sudo reboot
```
在 **Normal 模式** 下，KySec 会阻止未经授权的操作，包括关机命令。如果用户尝试执行 _shutdown_ 或 _reboot_，系统会提示需要授权或直接拒绝操作。
**模式选择**：KySec 提供三种模式： Normal：强制模式，阻止违规操作。 Warning：警告模式，仅提示用户。 Softmode：软模式，仅记录日志，不阻止操作。

3. 关闭kysec
	1. 临时关闭：`sudo setstatus disable && reboot`
	2. 永久关闭: `sudo setstatus disable -p && reboot`
	