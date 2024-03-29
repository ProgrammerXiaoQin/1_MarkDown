###  一. crontab
>crontab命令被用来提交和管理用户的定时任务。当安装完成Linux系统，会自动启动crond进程.  crond 服务(守护进程)在系统后台运行，并且会持续地检查 `/etc/crontab` 文件和 `/etc/cron.*/`目录。也会每分钟查看 `/var/spool/cron` 路径下用户名命名的定时任务文件, 以确定是否有需要执行的任务. `crontab`命令就是在修改`/var/spool/cron`中的定时任务文件

1. 语法
- 1 2 3 4 5 /path/to/command arg1 arg2
- 1 2 3 4 5 /root/ntp_sync.sh
- 前五个字段分别为分钟(minute 0-59), 小时(hour 0-23), 一月中的某天(day of month1-31), 月(mount 1-12),一周中的某天(day of week 0-6)
- 注:day of month和day of week一般不同时使用
- 注:定时任务中的命令要写全路径 , `/etc/crontab`中的path可能找不到
- 注:不要求命令或者脚本输出内容可以在后面输出重定向到`/dev/null`

2. 参数
	- `-e`  编辑该用户的计时器设置
	- `-l`   列出该用户的计时器设置
	- `-r`   删除该用户的计时器设置
	- `-u<用户名称>`  指定要设定计时器的用户名称
 
3. 操作符
	- `*`  为字段指定所有可用的值 , 在小时字段中，一个星号等同于每个小时；在月份字段中，一个星号则等同于每月
	- `,`  指定了一个包含多个值的列表，例如：`1,5,10,15,20,25`
	- `-`  指定了一个值的范围，例如：`5-15` 等同于`5,6,7....14,15`
	- `/`  指定了一个步进值,   例如： `0-23/` 可以用于小时字段来指定某个命令每小时被执行一次 , 步进值也可以跟在星号操作符后边，如果你希望命令行每 2 小时执行一次，则可以使用 `*/2`。

4. 特殊字符 , 可以使用特殊字符串中的其中一个替代头五个字段
	- `@reboot` 在每次启动时运行一次
	- `@yearly`    每年启动一次 ,同等与 `0 0 1 1 *`
	- `@monthly`  每月运行一次, 等同于 `0 0 1 * *`
	- `@weekly`    每周运行一次, 等同于`0 0 * * 0`
	- `@daily`     每天运行一次, 等同于`0 0 * * *`
	- `@hourly`   每小时运行一次, 等同于`0 * * * *`

6. 例
```crontab
*/5 * * * * /root/backupscript.sh
## 每五分钟执行一次脚本
0 1 * * * /root/backupscript.sh
## 每天凌晨一点运行一次脚本
15 3 1 * * /root/backupscript.sh
## 每月的第一个凌晨 3:15 运行 backupscript 脚本
@hourly /path/to/ntpdate
## 每小时运行一次 ntpdate 命令
0 7-11 * * * /root/backupscript.sh
## 每天早上七点到11点执行一次脚本
```

7. 查看crontab 执行日志 `tail -f /var/log/cron` 