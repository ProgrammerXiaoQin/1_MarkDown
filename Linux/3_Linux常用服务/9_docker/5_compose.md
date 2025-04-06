#### 1. 简介
`Compose` 定位是 「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」，其前身是开源项目 Fig

`Compose` 中有两个重要的概念：
1. 服务(service):  一个应用的容器，实际上可以包括若干运行相同镜像的容器实例
2. 项目(project): 由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义

#### 2. 安装与卸载
1. 安装
	- 二进制包安装 
	- pip安装
```bash
# 安装二进制包
$ sudo curl -L https://download.fastgit.org/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose

## 安装
sudo pip install -U docker-compose

## bash命令补全
$ curl -L https://raw.githubusercontent.com/docker/compose/1.27.4/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

2. 卸载
```bash
#卸载二进制包
$ sudo rm /usr/local/bin/docker-compose

##卸载pip安装的compose
$ sudo pip uninstall docker-compose
```

#### 3. 命令说明
1. 对于 Compose 来说 , 大部分命令的对象既可以是项目本身 , 也可以指定为项目中的服务或者容器. 如果没有特别的说明 , 命令对象将是项目 , 这意味着项目中所有的服务都会受到命令影响.

2. `docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]` 基本格式

3. 选项
	- `-f` 指定`compose`模板文件 , 默认为`docker-compose.yml`
	- `-p name`  只当项目名称 , 默认为所在目录名
	- `--verbose` 输出更过调试信息
	- `-v` 打印版本信息

4. 命令使用说明
	1. build `docker-compose build [options] [service..]` 构建（重新构建）项目中的服务容器 , 
		- `--force-rm`  删除构建过程中的临时容器
		- `--no-cache` 构建镜像过程中不使用 cache（这将加长构建过程）
		- `--pull` 始终尝试通过 pull 来获取更新版本的镜像
	2. config 验证 Compose 文件格式是否正确 , 若正确则显示配置 , 若格式错误显示错误原因.
	3. down 此命令将会停止 `up` 命令所启动的容器 , 并移除网络
	4. exec 进入指定的容器
	5. images 列出 Compose 文件中包含的镜像
	6. kill `docker-compose kill [options] [SERVICE...]` 通过发送 `SIGKILL` 信号来强制停止服务容器。
		1. `-s` 指定发送的信号 , 例如通过以下指令发送`SIGINT`信号 : `docker-compose kill -s SIGINT`
	7. logs `docker-compose logs [options] [SERVICE...]` 查看服务容器的输出
	8. pause `docker-compose pause [SERVICE...]` 暂停一个服务器
	9. port `docker-compose port [options] SERVICE PRIVATE_PORT` 打印某个容器端口所映射的公共端口
		- `--protocol=proto` 指定端口协议 , tcp(默认值)或者 udp
		- `--index=index` 如果同一服务存在多个容器 , 指定命令对象容器的序号(默认为 1)
	10. ps `docker-compose ps [options] [SERVICE...]` 列出项目中目前的所有容器
		- `-q` 只打印容器的 ID 信息
	11. pull `docker-compose pull [options] [SERVICE...]` 拉取服务依赖的镜像
	12. push 推送服务依赖的镜像到 Docker 镜像仓库
	13. restart `docker-compose restart [options] [SERVICE...]` 重启项目中的服务
	14. rm `docker-compose rm [options] [SERVICE...]` 删除所有(停止状态的)服务容器。推荐先执行 `docker-compose stop` 命令来停止容器
		- -f 强制直接删除 , 包括非停止状态的容器(不建议使用)
		- -v 删除容器所挂载的数据卷
	15. run `docker-compose run [options] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]` 在指定服务上执行一个命令
		- 例如 `$ docker-compose run ubuntu ping docker.com`
		- `-d` 后台运行容器
		- `--name NAME` 为容器指定名字
		- `--entrypoint CMD` 覆盖默认的容器启动指令
		- `-e KEY=VAL` 设置环境变量值 , 可多次使用选项来设置多个环境变量
		- `-u, --user=""` 指定运行容器的用户名或者 uid
		- `--no-deps` 不自动启动关联的服务容器
		- `--rm` 运行命令后自动删除容器，`d` 模式下将忽略
		- `-p, --publish=[]` 映射容器端口到本地主机
		- `--service-ports` 配置服务端口并映射到本地主机
		- `-T` 不分配伪 tty，意味着依赖 tty 的指令将无法运行
	16. scale `ocker-compose scale [options] [SERVICE=NUM...]` 设置指定服务运行的容器个数
		- `$ docker-compose scale web=3 db=2` 将启动 3 个容器运行 web 服务，2 个容器运行 db 服务
		- 当指定数目多于该服务当前实际运行容器 , 将新创建并启动容器; 反之 ,将停止容器 . 
		- `-t` 停止容器时候的超时(默认为 10 秒)
	17. start `docker-compose start [SERVICE...]` 启动已经存在的服务容器 , stop则为暂停
	18. top 查看各个服务容器内运行的进程
	19. unpause `docker-compose unpause [SERVICE...]` 恢复处于暂停状态中的服务
	20. up `docker-compose up [options] [SERVICE...]` (重新)创建服务，启动服务，并关联服务相关容器
		- `-d` 在后台启动并运行所有的容器
		- `--no-color` 不使用颜色来区分不同的服务的控制台输出
		- `--no-deps` 不启动服务所链接的容器
		- `--force-recreate` 强制重新创建容器，不能与 `--no-recreate` 同时使用。
		- `--no-recreate` 如果容器已经存在了，则不重新创建，不能与 `--force-recreate` 同时使用
		- `--no-build` 不自动构建缺失的服务镜像
		- `-t, --timeout TIMEOUT` 停止容器时候的超时(默认为 10 秒)

