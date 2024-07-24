#### *一. 简介*
1. 在生产环境中，我们基本上不会直接管理 pod，我们需要 `kubernetes` 来帮助我们来完成一些自动化操作
2. `deployment` 主要用于管理 pod。当`deployment` 管理点pod被删除后，k8s会自动创建`deployment` 定义的pod

#### *二. 配置和功能*
1. 滚动跟新：保证新版本的 pod 还没有 `ready` 之前，先不删除之前版本的 pod
	1. 在 deployment 的资源定义中, `spec.strategy.type` 有两种选择:
		- **RollingUpdate:** 逐渐增加新版本的 pod，逐渐减少旧版本的 pod。[官网定义](https://kubernetes.io/zh-cn/docs/concepts/workloads/controllers/deployment/)。
			- 滚动更新又可以通过 `maxSurge` 和 `maxUnavailable` 字段来控制升级 pod 的速率
			- `maxSurge` 最大峰值，用来指定可以创建的超出期望 Pod 个数的 Pod 数量。
			- `maxUnavailable` 最大不可用，用来指定更新过程中不可用的 Pod 的个数上限
		- **Recreate:** 在新版本的 pod 增加前，先将所有旧版本 pod 删除。
2. 存活探针 (livenessProb)
>存活探测器来确定什么时候要重启容器。 例如，存活探测器可以探测到应用死锁（应用程序在运行，但是无法继续执行后面的步骤）情况。 重启这种状态下的容器有助于提高应用的可用性，即使其中存在缺陷。-- [LivenessProb](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

 deployment 的定义:  这里使用存活探测方式是使用 HTTP GET 请求，请求的是刚才定义的 `/healthz` 接口，`periodSeconds` 字段指定了 kubelet 每隔 3 秒执行一次存活探测。 `initialDelaySeconds` 字段告诉 kubelet 在执行第一次探测前应该等待 3 秒。如果服务器上 `/healthz` 路径下的处理程序返回成功代码，则 kubelet 认为容器是健康存活的。 如果处理程序返回失败代码，则 kubelet 会杀死这个容器并将其重启

 ps: 代码中 `/healthz` 接口会在启动成功的 15s 内正常返回 200 状态码，在 15s 后，会一直返回 500 的状态码
```yaml
    spec:
      containers:
        - image: guangzhengli/hellok8s:liveness
          name: hellok8s-container
          livenessProbe:
            httpGet:
              path: /healthz
              port: 3000
            initialDelaySeconds: 3
            periodSeconds: 3
```

3. 就绪探针 (readiness)
> 就绪探测器可以知道容器何时准备好接受请求流量，当一个 Pod 内的所有容器都就绪时，才能认为该 Pod 就绪。 这种信号的一个用途就是控制哪个 Pod 作为 Service 的后端。 若 Pod 尚未就绪，会被从 Service 的负载均衡器中剔除。-- [ReadinessProb](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

[Probe](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#probe-v1-core) 有很多配置字段，可以使用这些字段精确地控制就绪检测的行为：
- `initialDelaySeconds`：容器启动后要等待多少秒后才启动存活和就绪探测器， 默认是 0 秒，最小值是 0。
- `periodSeconds`：执行探测的时间间隔（单位是秒）。默认是 10 秒。最小值是 1。
- `timeoutSeconds`：探测的超时后等待多少秒。默认值是 1 秒。最小值是 1。
- `successThreshold`：探测器在失败后，被视为成功的最小连续成功数。默认值是 1。 存活和启动探测的这个值必须是 1。最小值是 1。
- `failureThreshold`：当探测失败时，Kubernetes 的重试次数。 对存活探测而言，放弃就意味着重新启动容器。 对就绪探测而言，放弃意味着 Pod 会被打上未就绪的标签。默认值是 3。最小值是 1。

ps: 代码中 `/healthz` 接口会返回 500 状态码
```yaml
    spec:
      containers:
        - image: guangzhengli/hellok8s:bad
          name: hellok8s-container
          readinessProbe:
            httpGet:
              path: /healthz
              port: 3000
            initialDelaySeconds: 1
            successThreshold: 5
```


#### *三. 配置文件*
```yaml
apiVersion: apps/v1
#指定资源类型
kind: Deployment
metadata:
#deployment名称，这个名字是唯一的
  name: hellok8s-deployment
spec:
#最大可能会创建 4 个 hellok8s pod (replicas + maxSurge)，最小会有 2 个 hellok8s pod 存活 (replicas - maxUnavailable)
  strategy:
     rollingUpdate:
       maxSurge: 1
       maxUnavailable: 1
#部署的 pod 副本数量[[3_Deployment]]
  replicas: 3
#selector里面表示的是deployment资源和pod资源关联的方式，这里表示 deployment 会管理 (selector) 所有 labels=hellok8s 的 pod
  selector:
    matchLabels:
      app: hellok8s
#template的内容用来定义pod资源
  template:
#metadata.labels 来和上面的 selector.matchLabels 对应起来。来表明 pod 是被 deployment 管理，不用在template 里面加上 metadata.name 是因为 deployment 会自动为我们创建 pod 的唯一name。
    metadata:
      labels:
        app: hellok8s
    spec:
      containers:
        - image: guangzhengli/hellok8s:v1
          name: hellok8s-container

---------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hellok8s-deployment
spec:
  strategy:
     rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  replicas: 3
  selector:
    matchLabels:
      app: hellok8s
  template:
    metadata:
      labels:
        app: hellok8s
    spec:
      containers:
      - image: guangzhengli/hellok8s:v2
        name: hellok8s-container
```


#### *四. 常用命令*
1. `kubectl rollout undo deployment hellok8s-deployment`  回滚deployment
	- `--to-revision=3` 来回滚到指定版本。
2. `kubectl rollout history deployment hellok8s-deployment` 查看历史版本