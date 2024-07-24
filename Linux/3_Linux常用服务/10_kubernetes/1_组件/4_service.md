#### *一. 简介*
> 它为 pod 提供一个稳定的 Endpoint。Service 位于 pod 的前面，负责接收请求并将它们传递给它后面的所有pod。一旦服务中的 Pod 集合发生更改，Endpoints 就会被更新，请求的重定向自然也会导向最新的 pod。

#### *二. 类型*
1. *ClusterIP*  默认类型，集群内部的服务
2. *NodePort*  节点端口类型，将服务公开到集群节点上
	- 通过每个节点上的 IP 和静态端口（`NodePort`）暴露服务。 `NodePort` 服务会路由到自动创建的 `ClusterIP` 服务。 通过请求 `<节点 IP>:<节点端口>`，你可以从集群的外部访问一个 `NodePort` 服务。
3. *LoadBalancer*  负载均衡类型，将服务公开到外部负载均衡器上
	- 使用云提供商的负载均衡器向外部暴露服务。 外部负载均衡器可以将流量路由到自动创建的 `NodePort` 服务和 `ClusterIP` 服务上
4. *ExternaName*  外部名称类型，将服务映射到一个外部域名上
5. *Headless*  无头类型，主要用于DNS解析和服务发现


6. 通过配置文件
```yaml
#api版本
apiVersion: v1  
#资源类型
kind: Service
#源数据
metadata:
    name: nginx-service
#服务的规格和描述
spec:
	#执行服务类型，默认为clusterIP类型（内部服务），指定为nodeport类型可以提供对外访问
	type: NodePort
	#选择器，选择特定资源
    selector:
        app: nginx
    #对外公开端口
    ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      #对外端口(30000~32767)
      nodePort: 30080
```


