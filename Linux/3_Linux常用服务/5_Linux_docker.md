docker是基于Linux内核的Cgroups, NameSpace ,以及Union FS等技术 , 对进程进行封装隔离 , 属于操作系统层面的虚拟化技术

docker被定义为开源的容器引擎 , 可以方便对容器进行管理 , 包括镜像的打包封装 , 引入Docker Registry对镜像统一管理
#### 一. Docker架构
1. Docker 包括三个基本概念:
	-  **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
	-   **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
	-   **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。
#### 二. Docker安装
1. 配置yum源

3. 更新yum工具 `yum update`

4. 安装`yum install yum-utils device-mapper-persistent-data 1vm2`

5. 安装docker  `yum install docker-ce docker-ce-cli containerd.`
	- 安装时如果提示找不到`docker-ce`包可以更新一下索引, `yum makecache fast`

6. 启用docker
	-  systemctl enable docker
	- systemctl start docker

7. 建立docker用户组
	`docker` 命令会使用Unix socket与 Docker 引擎通讯。只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 `docker` 的用户加入 `docker` 用户组。
	- `groupadd docker` 建立 `docker` 组
	- usermod -aG docker $USER

8. 镜像加速
	- 在`/etc/docker/daemon.json`中添加:`{"registry-mirrors":["https://reg-mirror.qiniu.com/"]}`
	- 重启服务`systemctl daemon-reload && systemctl restart docker`

#### 三. Docker镜像
1. 获取镜像
	- `docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]`
	- Docker 镜像仓库地址 : 地址的格式一般是 `<域名/IP>[:端口号]`。默认地址是 Docker Hub(`docker.io`)。
	- 这里的仓库名是两段式名称，即 `<用户名>/<软件名>`。对于 Docker Hub，如果不给出用户名，则默认为 `library`，也就是官方镜像。
	- `docker pull ubuntu:18.04` 可写成`docker pull docker.io/library/ubuntu:18.04

2. 运行
	- docker run -it --rm ubuntu:18.04 bash
	- `-it`  -i:交互式操作 , -t:终端 , 进入交互界面
	- `--rm`  容器退出后随之将其删除
	- `bash`  放在镜像名后的命令，这里我们需要有个交互式 Shell，因此用的是bash
	- `docker run --name webserver -d -p 8081:80 nginx`
	- `--name webserver` 将容器命名为webserver
	- `-p 8081:80` 将主机的8081端口映射到容器的端口
	- `-d` 后台运行容器，并返回容器ID

3. 列出镜像`docker image ls [imagename[:标签]]`
	1. `-a` 列出所有镜像(包括中间层镜像)
	2. 指定`imagename`根据仓库名列出镜像
	3. `docker system df` 查看镜像, 容器 , 数据卷占用空间
	4. `-q` 只列出镜像id

4. 查看镜像体积
	- `docker system df`

6. 虚悬镜像
	1. 随着官方镜像维护，发布了新版本后，重新 `docker pull`时镜像名被转移到了新下载的镜像身上，而旧的镜像上的这个名称则被取消，从而成为了 `<none>`。除了 `docker pull` 可能导致这种情况，`docker build` 也同样可以导致这种现象。这类镜像被称为虚悬镜像
		- `docker image ls -f dangling=true` 列出虚悬镜
		- `docker image prune` 删除虚悬镜

7. 删除镜像
	- `docker image rm [选项] <镜像1> [<镜像2> ...]` or `docker rmi ...`
	- 其中，`<镜像>` 可以是 `镜像短ID(一般用id前个字符)`、`镜像长ID`、`镜像名(<仓库名>:<标签>)` 或者 `镜像摘要(docker image ls --digests查看)`

9. 查看镜像构建的历史纪录
	- `docker history 镜像名:tag`


#### 三. Docker容器
1. 查看容器
	- `docker ps [选项]`
	- 查看正在运行的容器
	- `-a`  查看所有容器 , 包括以停止的容器
	- `-q`  只列出镜像id

2. 进入容器 `docker exec [OPTIONS] 容器名`
	- `docker exec -it webserver bash`
	- `-i` 即使没有附加也保持STDIN 打开
	- `-t` 分配一个伪终端
	- `bash`  将bash分配为伪终端

3. 停止正在运行的容器
	- `docker stop 容器名`

4. 删除容器 
	-  `docker rm 容器名`

5. 查看容器改动内容
	- `docker diff 容器名`

6. 将容器构建为镜像 `docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]`
	- `docker commit --author "Tao Wang <twang2218@gmail.com>" --message "修改了默认网页" webserver nginx:v2` 	
	- `--author` 指定修作者
	- `--message` 记录本次修改内容
	- 注: `docker commit` 实在原有镜像基础上构建一层新的镜像 , 如果用`docker diff` 命令查看修改过的结果 , 会发现除了真正修改的东西外还有很多文件被改动或添加 , 这会导致镜像极为臃肿 , 并且使用`docker commit`修改的镜像除了本人外无法得知真正的对镜像的改动 , 所以生产时一般不用

#### 四.  使用dockerfile定制镜像
1. 说明: Dockerfile 是一个文本文件，其内包含了一条条的 **指令(Instruction)**，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建
2. 语法
	 - `FROM` 指定基础镜像 , 在其上进行定制 , 是dockerfile中必备的第一条指令,如果不相以任何镜像为基础可以用`FROM scratch` 
		- `FROM ubuntu:18.04`
	- `RUN` 是用来执行命令的指令 , 其格式有两种:
		- `RUN <命令>` 
		- `RUN ["可执行文件", "参数1", "参数2"]` 
		- `RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html`
		- 由于dockerfile中每一个指令都会新建一层镜像 , 所以当有多个指令执行的时候应该用`&&`链接多个命令  , Dockerfile 支持 Shell 类的行尾添加 `\` 的命令换行方式，以及行首 `#` 进行注释的格式。
		- 确保每一层镜像都只添加真正想的东西 , 做好在run最后删除无关紧要的文件
	- `COPY` 指令将从构建上下文目录中 `<源路径>` 的文件/目录复制到新的一层的镜像内的 `<目标路径>` 位置。
		- `COPY [--chown=<user>:<group>] <源路径>... <目标路径>`
		- `COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]`

3. 构建镜像
	- `docker build [选项] <上下文路径/URL/->`
	- `<上下文路径/URL/->` : dockerfile所在路径
	- `docker build -t nginx:v3 .`
	- `-t` : 指定镜像名称
	- `docker build` 命令构建镜像，并非在本地构建，而是在服务端Docker 引擎中构建 , 构建的时候，用户会指定构建镜像上下文的路径，`docker build` 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎
```
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```