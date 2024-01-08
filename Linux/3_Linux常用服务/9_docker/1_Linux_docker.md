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
	- 由于 CentOS8 防火墙使用了 `nftables`，但 Docker 尚未支持 `nftables`， 我们可以使用如下设置使用 `iptables`：
```
$ firewall-cmd --permanent --zone=trusted --add-interface=docker0
$ firewall-cmd --reload
```

6. 启用docker
	- systemctl enable docker
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
	- `docker run -it --rm ubuntu:18.04 bash`
	- `-it`  -i:交互式操作 , -t: 让Docker分配一个伪终端并绑定到容器的标准输入上
	- `--rm`  容器退出后随之将其删除
	- `bash`  容器启动后需执行的命令，这里我们需要有个交互式 Shell，因此用的是bash
	- `docker run --name webserver -d -p 8081:80 nginx`
	- `--name webserver` 将容器命名为webserver
	- `-p 8081:80` 将主机的8081端口映射到容器的80端口
	- `-d` 后台运行容器，并返回容器ID
	- `-v /test:/soft` 将宿主机`/test` 目录挂在到容器`/soft`目录 , 宿主机目录若是不存在则会自动生成 , 若宿主机使用相对目录 ,如`-v test1:/soft`则会在宿主机`/var/lib/docker/volumes/`目录下生成一个test1
	- `--restart=`
		- no：容器退出时不重启(默认)
		- on-failure：容器故障退出（返回值非零）时重启 , `on-failure:3`, 自动重启三次 ,如果还是错误则不再重启
		- always：容器退出时总是重启
	- 其他高级参数
		- `-u` 指定容器用户
		- `-c, --cpu-shares=0`， 设置容器CPU权重，在CPU共享场景使用
		- `-m, --memory=""`， 指定容器的内存上限 
		- `-h, --hostname=""`， 指定容器的主机名
		- `--dns=[]`， 指定容器的dns服务器
		- `-e,--env` , 指定环境变量 , 格式为`-e key=value` , 多次指定设置多个

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

10. 导入,导出镜像
	- `docker save alpine -o filename` ,将镜像alpine保存为文件
	- `docker save alpine | gzip > alpine-latest.tar.gz` ,将镜像保存为文件并压缩
	- `docker load -i alpine-latest.tar.gz` 导入镜像存储文件到本地镜像库
	- `docker save <镜像名> | bzip2 | pv | ssh <用户名>@<主机名> 'cat | docker load'` 将镜像迁移到另一个机器
	- `docker load` 导入镜像存储文件到本地镜像库，也可以使用`docker import` _来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。
#### 四. Docker容器
1. 查看容器
	- `docker ps [选项]`
	- 查看正在运行的容器
	- `-a`  查看所有容器 , 包括以停止的容器
	- `-q`  只列出镜像id

2. 进入容器 `docker exec [OPTIONS] <容器名>` 或`docker attach <容器名>`
	- `docker exec -it webserver bash`
	- `-i` 即使没有附加也保持STDIN 打开 , 也可以返回输出结果 , 但是没有提示符
	- `-t` 分配一个伪终端
	- `bash`  将bash分配为伪终端
	- `docker attach webserver`
	- 进入webserver容器 , 用attach进入容器 , 输入exit会退出并会导致容器停止 , exec则只会退出
	- `docker exec` 还可以让容器执行指定命令 , 如`docker exec -it nginx01 env` 将查看nginx容器的环境变量

3. 操作容器暂停 , 重启 , 启动
	- `docker stop 容器名` 或 `docker container stop <容器名>` 终止正则运行的容器
	- `docker container start <容器名>` 启动一个已经被终止(exited)的容器
	- `docker container restart <容器名>` 重启正在运行的容器

4. 删除容器 
	- `docker rm 容器名`
	- `docker container prune` 清理所有处于终止状态的容器

5. 查看容器改动内容
	- `docker diff 容器名`

6. 将容器构建为镜像 `docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]`
	- `docker commit --author "Tao Wang <twang2218@gmail.com>" --message "修改了默认网页" webserver nginx:v2` 	
	- `--author` 指定修作者
	- `--message` 记录本次修改内容
	- 注: `docker commit` 实在原有镜像基础上构建一层新的镜像 , 如果用`docker diff` 命令查看修改过的结果 , 会发现除了真正修改的东西外还有很多文件被改动或添加 , 这会导致镜像极为臃肿 , 并且使用`docker commit`修改的镜像除了本人外无法得知真正的对镜像的改动 , 所以生产时一般不用

7. 查看容器虚拟终端输出`docker logs <容器名>`  或 `docker container logs <容器名>` 

8. 导入导出
	- `docker export <容器ID> > ubuntu.tar` 导出容器快照到本地
	- `cat ubuntu.tar | docker import - test/ubuntu:v1.0` 导入容器快照为镜像,`test/ubuntu:v1.0`分别为`用户名/软件名:标签`
	- `docker import [选项] <文件>|<URL>|- [<仓库名>[:<标签>]]`
	- 从网络或本地容导入器快照到镜像

9. 查看容器状态: `docker stats <容器名>`

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
	- `COPY <src> <dest>` 复制本地主机`<src>`下内容到镜像的`<dest>` , 目标路径不存在时会自动创建
		- `<src>` 本地工作路径(build时所指定)下的文件或目录 , 可以是多个，甚至可以是通配符
		- `<dest>`可以是镜像内绝对路径，或者相对于工作目录(WORKDIR)的相对路径
		- `COPY [--chown=<user>:<group>] <源路径>... <目标路径>`
		- `COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]`
		- 使用 `COPY` 指令，源文件的各种元数据都会保留。比如读、写、执行权限、文件变更时间等。`--chown=<user>:<group>` 选项来改变文件的所属用户及所属组。
	- `ADD <src> <dest>`
		- `ADD` 指令和 `COPY` 的格式和性质基本一致 . 但是在 `COPY` 基础上增加了一些功能
		- `<src>` 可以是一个 URL , Docker 引擎会试图去下载这个链接的文件放到 `<dest>` 去 , 下载后的文件权限自动设置为 `600`
		- 如果 `<源路径>` 为一个 `tar` 压缩文件的话，压缩格式为 `gzip`, `bzip2` 以及 `xz` 的情况下，`ADD` 指令将会自动解压缩这个压缩文件到 `<目标路径>` 去
		- 尽可能的使用 `COPY` , `ADD` 指令会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。
	- `CMD` 与 `RUN` 相似，也是两种格式:
		- `shell` 格式：`CMD <命令>`
		- `exec` 格式：`CMD ["可执行文件", "参数1", "参数2"...]`
		- `CMD` 指令用于指定默认的容器主进程的启动命令
		- 例如执行 `docker run -it ubuntu` 的话(Ubuntu镜像默的CMD是BASH)，会直接进入 `bash`。我们也可以在运行时指定运行别的命令，如 `docker run -it ubuntu cat /etc/os-release`。这就是用 `cat /etc/os-release` 命令替换了默认的 `/bin/bash` 命令了，输出了系统版本信息
		- `exec` 格式在解析是会被解析成JSON数组 , 因此要使用`"` 而不是单引号
		- `shell` 格式会被包装成`sh -c`参数的形式进行执行 , 如
			- `CMD echo $HOME` 会被包装成`CMD [ "sh", "-c", "echo $HOME" ]`
	- `ENTRYPOINT` `ENTRYPOINT` 的目的和 `CMD` 一样，都是在指定容器启动程序及参数
		- 格式 : 同`CMD`
		- 当指定 `ENTRYPOINT` 后，`CMD` 的含义就发生了改变，不再是直接的运行其命令，而是将 `CMD` 的内容作为参数传给 `ENTRYPOINT` 指令
			- `<ENTRYPOINT> "<CMD>"`
	- `ENV` 用于设置环境变量 , 后面的其他指令可以直接使用这里的环境变量
		- 格式: `ENV <key> <value>` 或 `ENV <key1>=<value1> <key2>=<value2>`
	- `ARG` 用于设置构建环境的环境变量 , 容器运行时不会存在这些环境变量
		- 格式: 同`ENV`
		- 其参数值可在构建命令`docker build`中用`--build-arg <参数名>=<值>`覆盖
		- `ARG` 指令有生效范围，如果在 `FROM` 指令之前指定，那么只能用于 `FROM` 指令中
	- `VOLUME` 定义匿名卷
		- 格式: `VOLUME ["<路径1>", "<路径2>"]` 或 `VOLUME <路径>`
		- 容器运行时应该尽量保持容器存储层不发生写操作 , 对于数据库这种需要保存动态数据的应用 , 其数据应该保存在卷(volume)中,  为了防止运行时用户忘记将动态文件所保存目录挂载为卷 , 在 `Dockerfile` 中可以事先指定某些目录为挂在为匿名卷 , 这样容器运行时如果用户不指定挂 , 其应用也不会向容器层写入大量数据
		- `VOLUMU /data`  `/data` 目录就会在容器运行时自动挂载为匿名卷，任何向 `/data` 中写入的信息都不会记录进容器存储层
		- 容器启动时`docker run` `-v` 参数会覆盖该设置
	- `EXPOSE` 暴露端口
		- 格式:  `EXPOSE <端口1> [<端口2>...]`
		- `EXPOSE` 命令是声明容器运行时提供的服务端口 , 并不是真正会开启这个端口 , 该命令用于使用者方便配置映射
	- `WORKDIR` 指定工作目录
		- 格式: `WORKDIR <工作目录路径>`
		- 当用`WORKDIR`指定目录后以后各层的当前目录就被改为工作目录 , 若该目录不存在则帮你自动创建
		- `WORKDIR` 使用相对路径 , 那么所切换的路径与之前的`WORKDIR`相关
	- `USER` 指定当前用户
		- 格式: `USER <用户名>[:用户组]`
		- `USER` 只是帮助你切换到指定用户而已，这个用户必须是事先建立好的，否则无法切换

3. 构建镜像
	- `docker build [选项] <本地工作路径/URL/->`
	- `<本地工作路径/URL/->` : dockerfile中COPY , ADD等操作的本地工作路径
	- `docker build -t nginx:v3 .`
	- `-f` : 指定dockerfile 
	- `-t` : 指定镜像名称 
	- `docker build` 命令构建镜像，并非在本地构建，而是在服务端Docker 引擎中构建 , 构建的时候，用户会指定构建镜像上下文的路径，`docker build` 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎
	- `docker build http://server/context.tar.gz`
	- 如果所给出的URL是个 `tar` 压缩包，那么 Docker 引擎会下载这个包，并自动解压缩，以其作为上下文，开始构建
```
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

4. 多阶构建
	1. 说明: 对于类似需要编译后执行的程序 , 如go语言程序 , 如果我们将所有的构建过程包含在一个`Dockerfile`中 , 包括项目及依赖库的编译 , 测试 , 打包等流程, 会导致镜像层次过多 , 镜像体积大 , 部署时间长 , 源代码还有泄露风险 .
	2. 例: app.go文件需要编译发布
```dockerfile
## 编译并运行
FROM golang:alpine

RUN apk --no-cache add git ca-certificates

WORKDIR /go/src/github.com/go/helloworld/

COPY app.go .

RUN go get -d -v github.com/go-sql-driver/mysql \
  && CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app . \
  && cp /go/src/github.com/go/helloworld/app /root

WORKDIR /root/

CMD ["./app"]
```
我们可以使用多阶段构建 , 如下
```dockerfile
FROM golang:alpine as builder

RUN apk --no-cache add git

WORKDIR /go/src/github.com/go/helloworld/

RUN go get -d -v github.com/go-sql-driver/mysql

COPY app.go .

RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest as prod

RUN apk --no-cache add ca-certificates

WORKDIR /root/

## copy --from=0表是从上一阶段的镜像中复制文件，我们也可以复制任意镜像中的文件
COPY --from=0 /go/src/github.com/go/helloworld/app .

CMD ["./app"]
```

#### 五. 其他docker命令
1. docker hub相关
	- `https://hub.docker.com` 官网
	- `docker login` 命令行登录 , `docker logout` 退出登录
	- `docker search` 查询官方仓库镜像
	- `docker push username/ubuntu:18.04` 登录后将自己的镜像推送到docker hub , username为自己的用户名
		- 注: 推送前请打上标签 , 如`docker tag ubuntu:18.04 username/ubuntu:18.04` , 指定ubuntu:18.04镜像 , 推送到username用户下的仓库 , 软件名为Ubuntu , 标签为18.04

2. 文件相关
	- `docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH`
	- `CONTAINER` 可以是正在运行或已停止的容器，名称或ID均可
	- `SRC_PATH` 源文件或目录在容器内的路径
	- `DEST_PATH` 目标路径在主机上的位置
	- `[OPTIONS]`
		- `-a`：将复制的文件或目录保留其原始属性，包括所有者、权限等。
		- `-L`：如果SRC_PATH是一个符号链接，则复制链接指向的文件或目录。
		- `-p`：保留源文件或目录的时间戳。
		- `-R`或`-r`：递归复制整个目录

3. 查看镜像或容器信息 
	-  `docker inspect [容器或镜像id]` ,也可以用容器名或镜像名