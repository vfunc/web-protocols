# 如何将容器镜像部署到K8s

### 安装 Kubernetes

`brew install kind`

### 创建 K8s 集群

config.yaml

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
```

```
kind create cluster --config config.yaml
```

### 初识 Kubernetes

- Manifest 是清单文件，好比餐厅的菜单，你只管点菜，做菜的过程我不管。
- Kubectl 是一个与 K8s 集群交互的工具，通过 Kubectl，我们可以非常方便地以 Manifest 为媒介操作 K8s 集群的对象。
- Pod 是一组容器的集合，这些容器被统一安排和调度，并运行在共享的上下文中。Pod 是 K8s 调度的最小单位。

### 部署容器镜像到 K8s

flask-pod.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: hello-world-flask
spec:
  containers:
    - name: flask
      image: lyzhang1999/hello-world-flask:latest
      ports:
        - containerPort: 5000
```

- Kind 字段表示 K8s 的工作负载类型
- Metadata 节点下的 Name 字段表示工作负载的名字
- Containers 字段代表 Pod 要运行的容器配置
- Image 字段表示容器镜像
- Ports 字段表示容器在集群内部暴露的端口

```
kubectl apply -f flask-pod.yaml
```

查看 Pod

```
kubectl get pods
```

端口转发

```
kubectl port-forward pod/hello-world-flask 8000:5000
```

进入到 Pod 容器内部

```
kubectl exec -it hello-world-flask -- bash
```

删除 Pod

```
kubectl delete pod hello-world-flask
```

> 此文章为 4 月 Day18 学习笔记，内容来源于极客时间[云原生架构与 GitOps 实战](http://gk.link/a/121Vm)，强烈推荐该课程！
