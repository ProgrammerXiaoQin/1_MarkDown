#### 1. 外部访问容器
1. 容器可以运行网络应用，让外部访问这些应用
	- `docker run -d -p 8080:80 ` `-p` 将主机的8080端口映射到容器的80端口
	- `docker run -d -P nginx:alpine` `-P` 将本地主机随机端口映射到容器开放端口
	- `hostPort:containerPort` 这种格式默认会绑定本地所有接口上的所有地址。
	- `ip:hostPort:containerPort` 格式指定映射使用一个特定地址，比如 localhost 地址 127.0.0.1(此时只有本机可以访问这个端口)。如 `$docker run -d -p 127.0.0.1:80:80 nginx:alpine` 还可以使用`-p 127.0.0.1:80:80/udp` 来指定udp端口

#### 2. docker网络类型
1. bridge网络（默认网络）
	- 隔离性：同一主机内容器间通过虚拟网桥（`docker0`）通信。
	- NAT 机制：容器通过宿主机 IP 和端口映射（`-p`）与外部通信
	- DNS 支持：自定义 `bridge` 网络支持容器名称到 IP 的自动解析
	- 适用于单主机环境下多个容器通信，需要端口映射暴露服务到宿主机
```
# 创建自定义 bridge 网络
docker network create my-bridge

# 运行容器并加入自定义 bridge 网络
docker run -d --name web --network my-bridge nginx
docker run -d --name app --network my-bridge my-app
```

2. host网络
	- 无隔离：容器直接共享宿主机的网络命名空间，使用宿主机 IP 和端口
	- 无需 NAT 或端口映射，网络性能接近原生
	- 容器与宿主机或其他容器可能因端口冲突导致启动失败
```
# 运行容器并使用host网络
docker run -d --name web --network host nginx
```

3. none网络
	- 无网络：容器仅有本地环回接口（`lo`），无法与外部通信
	- 适用于不需要网络连接的场景
```
# 运行容器并使用none网络
docker run -d --name isolated-container --network none alpine sleep 3600
```

4. 常用网络命令
```
# 查看所有网络
docker network ls

# 查看网络详情
docker network inspect <网络名>

# 删除网络
docker network rm <网络名>
```


#### 3. 配置DNS
1. 配置全部容器DNS可以在`/etc/docker/daemon.json` 文件中增加以下内容
```
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}

#验证dns是否配置生效
$ docker run -it --rm ubuntu:18.04  cat etc/resolv.conf

```

2. 指定容器配置
	- `--dns=IP_ADDRESS` 添加 DNS 服务器到容器的 `/etc/resolv.conf` 中，docker run时