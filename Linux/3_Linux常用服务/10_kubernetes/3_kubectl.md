#### 1. 基础 
1. Kubctl 命令是操作 kubernetes 集群的最直接的方式
2. 安装
	- centos
```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
EOF

sudo yum install -y kubectl
```
> 安装可参考 ：https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management

#### 2. 使用
1. 常用命令
	1. 临时端口映射
		-  `kubectl port-forward nginx-pod 4000:80` 将主机端口4000临时映射给pod nginx-pod的80端口
	2. 进入 Pod 内容器的 Shell `kubectl exec -it`
		- `kubectl exec -it nginx-pod /bin/bash`  进入pod nginx-pod的shell
1. 资源创建和运行
```bash
# 创建并运行一个指定的镜像  
kubectl run NAME --image=image [params...]  
# e.g. 创建并运行一个名字为nginx的Pod  
kubectl run nginx --image=nginx  
  
# 根据YAML配置文件或者标准输入创建资源  
kubectl create RESOURCE  
# e.g.  
# 根据nginx.yaml配置文件创建资源  
kubectl create -f nginx.yaml  
# 根据URL创建资源  
kubectl create -f https://k8s.io/examples/application/deployment.yaml  
# 根据目录下的所有配置文件创建资源  
kubectl create -f ./dir  
  
# 通过文件名或标准输入配置资源  
kubectl apply -f (-k DIRECTORY | -f FILENAME | stdin)  
# e.g.  
# 根据nginx.yaml配置文件创建资源  
kubectl apply -f nginx.yaml
```

3. 资源的修改、删除和清理
```bash
# 更新某个资源的标签  
kubectl label RESOURCE NAME KEY_1=VALUE_1 ... KEY_N=VALUE_N  
# e.g. 更新名字为nginx的Pod的标签  
kubectl label pod nginx app=nginx  
  
# 删除某个资源  
kubectl delete RESOURCE NAME  
# e.g. 删除名字为nginx的Pod  
kubectl delete pod nginx  
  
# 删除某个资源的所有实例  
kubectl delete RESOURCE --all  
# e.g. 删除所有Pod  
kubectl delete pod --all  
  
# 根据YAML配置文件删除资源  
kubectl delete -f FILENAME  
# e.g. 根据nginx.yaml配置文件删除资源  
kubectl delete -f nginx.yaml  
  
# 设置某个资源的副本数  
kubectl scale --replicas=COUNT RESOURCE NAME  
# e.g. 设置名字为nginx的Deployment的副本数为3  
kubectl scale --replicas=3 deployment/nginx  
  
# 根据配置文件或者标准输入替换某个资源  
kubectl replace -f FILENAME  
# e.g. 根据nginx.yaml配置文件替换名字为nginx的Deployment  
kubectl replace -f nginx.yaml
```

4. 查看资源信息
```bash
# 查看集群中某一类型的资源  
kubectl get RESOURCE  
# 其中，RESOURCE可以是以下类型：  
kubectl get pods / po         # 查看Pod  
kubectl get svc               # 查看Service  
kubectl get deploy            # 查看Deployment  
kubectl get rs                # 查看ReplicaSet  
kubectl get cm                # 查看ConfigMap  
kubectl get secret            # 查看Secret  
kubectl get ing               # 查看Ingress  
kubectl get pv                # 查看PersistentVolume  
kubectl get pvc               # 查看PersistentVolumeClaim  
kubectl get ns                # 查看Namespace  
kubectl get node              # 查看Node  
kubectl get all               # 查看所有资源  
  
# 后面还可以加上 -o wide 参数来查看更多信息  
kubectl get pods -o wide  
  
# 查看某一类型资源的详细信息  
kubectl describe RESOURCE NAME  
# e.g. 查看名字为nginx的Pod的详细信息  
kubectl describe pod nginx
```