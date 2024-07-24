#### *一. 简介*
1.  Kubernetes 中可创建和管理的、最小的可部署的计算单元
2. `container` 和 `pod` 的区别：服务运行在 `container` 容器当中， `container` (容器) 的本质是进程，而 `pod` 是管理这一组进程的资源。
3. `pod` 可以管理多个 `container`


#### *二. Pod常用命令*
```bash
#e.g. 追踪nginx-pod的日志，--follow，类似于tail -f
kubectl logs --follow nginx-pod

#e.g. 调用容器命令ls，进入容器则exec -it -- /bin/bash
kubectl exec nginx-pod -- ls

#e.g. 删除pod nginx-pod
kubectl delete pod nginx-pod
#e.g. 根据配置文件删除资源
kubectl delete -f nginx.yaml

```

#### *三. 配置文件*
```yaml
apiVersion: v1
#指定资源类型
kind: Pod
metadata:
#pod名称
  name: hellok8s
spec:
#容器的名称和镜像名称
  containers:
    - name: hellok8s-container
      image: guangzhengli/hellok8s:v1

# nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
```
