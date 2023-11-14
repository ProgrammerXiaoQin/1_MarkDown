##### 1. 安装
1. window安装
	- `winget install Microsoft.devtunnel` 通过winget安装
	- `Invoke-WebRequest -Uri https://aka.ms/TunnelsCliDownload/win-x64 -OutFile devtunnel.exe` , `.\devtunnel.exe -h` 通过powershell安装
2. Ubuntu安装
	- `curl -sL https://aka.ms/DevTunnelCliInstall | bash`
3. Centos下载
	- `curl -O https://aka.ms/TunnelsCliDownload/linux-x64`

##### 2. 用户凭据
1. `devtunnel user login` 使用Micorosoft登录
	- `-g` 使用GitHub账户登录
	- `-d` 通过验证码方式登录
1. `devtunnel user logout` 清除缓存的令牌
2. `devtunnel user show`  显示当前登录状态

##### 2. 托管隧道开发
1. `devtunnel host -p 3000` 为在主机系统上侦听端口 3000 的服务器托管临时开发隧道
	- `-a / --allow-anonymous` 启用匿名访问
	- `-p 3000 5000` 为侦听端口 3000 和 5000 的本地服务器托管临时开发隧道


##### 3. 其他高级命令
|  命令 | 说明 |
|---|---|
|`devtunnel create`|创建持久性开发隧道|
|`devtunnel list`|列出开发隧道|
|`devtunnel show`|显示开发隧道详细信息|
|`devtunnel update`|更新开发隧道属性|
|`devtunnel delete`|删除开发隧道|
|`devtunnel delete-all`|删除所有开发隧道|

实列

|命令|说明|
|---|---|
|`devtunnel create -a`|创建允许匿名访问的持久性开发隧道。|
|`devtunnel create -d 'my tunnel description'`|创建具有不可搜索说明的持久开发隧道。|
|`devtunnel create --expiration 4h`|创建具有自定义过期时间的持久开发隧道。 最小值为 1 小时（1 小时），最大值为 30 天（30d）。|
|`devtunnel create --tags my-web-app v1`|创建持久开发隧道并应用可搜索标记。|
|`devtunnel list --tags my-web-app`|列出具有任何指定标记的开发隧道。|
|`devtunnel list --all-tags my-web-app v1`|列出具有所有指定标记的开发隧道。|
|`devtunnel show`|显示上次使用的开发隧道的详细信息。|
|`devtunnel show TUNNELID`|显示开发隧道的详细信息。|
|`devtunnel update TUNNELID -d 'my new tunnel description'`|更新开发隧道的说明。|
|`devtunnel update TUNNELID --remove-tags`|从开发隧道中删除所有标记。|
|`devtunnel update TUNNELID --expiration 10d`|使用新的自定义过期时间更新开发隧道。 最小值为 1 小时（1 小时），最大值为 30 天（30d）。|
|`devtunnel delete TUNNELID`|删除开发隧道。|
|`devtunnel delete-all`|删除所有开发隧道。|

##### 4. 管理开发隧道访问
|示例|说明|
|---|---|
|`devtunnel token TUNNELID --scopes connect`|获取可共享的开发隧道的“连接”访问令牌，以便暂时访问开发隧道。|
|`devtunnel access create TUNNELID --anonymous`|在开发隧道上启用匿名客户端访问。|
|`devtunnel access create TUNNELID --port 3000 --anonymous`|在端口 3000 上启用匿名客户端访问。|
|`devtunnel access create TUNNELID --tenant`|在开发隧道上启用当前的 Microsoft Entra 租户访问。|
|`devtunnel access create TUNNELID --org ORG`|在开发隧道上按名称启用 GitHub 组织访问|
