#### *一. 简介*
> [Ingress](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#ingress-v1beta1-networking-k8s-io) 公开从集群外部到集群内[服务](https://kubernetes.io/docs/concepts/services-networking/service/)的 HTTP 和 HTTPS 路由。 流量路由由 Ingress 资源上定义的规则控制。Ingress 可为 Service 提供外部可访问的 URL、负载均衡流量、 SSL/TLS，以及基于名称的虚拟托管。你必须拥有一个 [Ingress 控制器](https://kubernetes.io/zh-cn/docs/concepts/services-networking/ingress-controllers) 才能满足 Ingress 的要求。 仅创建 Ingress 资源本身没有任何效果。 [Ingress 控制器](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers) 通常负责通过负载均衡器来实现 Ingress，例如 `minikube` 默认使用的是 [nginx-ingress](https://minikube.sigs.k8s.io/docs/tutorials/nginx_tcp_udp_ingress/)，目前 `minikube` 也支持 [Kong-Ingress](https://minikube.sigs.k8s.io/docs/handbook/addons/kong-ingress/)。

1. Ingress 可以“简单理解”为服务的网关 Gateway，它是所有流量的入口，经过配置的路由规则，将流量重定向到后端的服务。

匹配前缀为 `/hello` 的路由规则，重定向到 `hellok8s:v3` 服务，匹配前缀为 `/` 的根路径重定向到 `nginx` ：
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-ingress
  annotations:
    # We are defining this annotation to prevent nginx
    # from redirecting requests to `https` for now
    #关闭 `https` 连接，只使用 `http` 连接
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
    - http:
        paths:
          - path: /hello
            pathType: Prefix
            backend:
              service:
                name: service-hellok8s-clusterip
                port:
                  number: 3000
          - path: /
            pathType: Prefix
            backend:
              service:
                name: service-nginx-clusterip
                port:
                  number: 4000
```