##### 1. win10系统迁移克隆分区后启动蓝屏，黑屏，只剩鼠标
1. 进入PE，运行RegEdit调出注册表，选中 `HKEY_LOCAL_MACHINE`
2. 打开菜单操作：文件-加载配置单元，找到系统目录下 `\windows\system32\config\`，选择system文件
3. 在弹出的对话框中输入sparkle，然后进入注册表sparkle
4. 删除mountedevices里面的内容，重启后自动恢复


