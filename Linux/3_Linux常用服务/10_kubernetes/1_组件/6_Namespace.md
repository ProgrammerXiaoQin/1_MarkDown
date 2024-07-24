#### *一. 简介* 
> 在 Kubernetes 中，**名字空间（Namespace）** 提供一种机制，将同一集群中的资源划分为相互隔离的组。 同一名字空间内的资源名称要唯一，但跨名字空间时没有这个要求。 名字空间作用域仅针对带有名字空间的对象，例如 Deployment、Service 等。

#### *二. Pod常用命令*
```bash
#获取namespaces
kubectl get namespaces

#在指定命名空间创建资源
kubectl apply -f deployment.yaml -n dev

```


#### *三. 配置文件*
创建两个不同的namespace，分别为`dev` 和 `test`.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
  
---

apiVersion: v1
kind: Namespace
metadata:
  name: test
```