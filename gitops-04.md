# 如何实现应用秒级自动发布和回滚

### 传统 K8s 应用发布流程

在发布应用的过程中，一般会先修改 Manifest 镜像版本，再使用 kubectl apply 重新将 Manifest 应用到集群来更新应用。新老版本的切换会不会导致服务中断，并且 K8s 将会自动处理，无需人工干预。通常，更新应用可以使用下面三种方式：

* 通过 kubectl set image 命令更新应用

```
$ kubectl set image deployment/hello-world-flask hello-world-flask=lyzhang1999/hello-world-flask:v1
```

* 通过修改本地的 Manifest 更新应用

```
$ kubectl apply -f new-hello-world-flask.yaml
```

* 通过修改集群内 Manifest 更新应用

```
$ kubectl edit deployment hello-world-flask
```

总结来说，要更新 K8s 的工作负载，我们可以修改本地的 Manifest，再使用 kubectl apply 将它重新应用到集群内，或者通过 kubectl edit 命令直接修改集群内已存在的工作负载。

### 搭建 GitOps 发布工作流

GitOps 就是以 Git 版本控制为理念的 DevOps 实践。对于 GitOps 发布工作流来说，我们会将 Manifest 存储在 Git 仓库中作为期望状态，一旦修改并提交了 Manifest，那么 GitOps 工作流就会自动比对 Git 仓库和集群内工作负载的实际差异，并进行部署。要实现 GitOps 工作流，首先我们需要一个能够帮助我们监听 Git 仓库变化，自动部署的工具。

#### 在集群内安装 FluxCD

```
$ kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/lyzhang1999/resource/main/fluxcd/fluxcd.yaml
$ kubectl wait --for=condition=available --timeout=300s --all deployments -n flux-system
```

#### 创建 fluxcd-demo 仓库

deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: hello-world-flask
  name: hello-world-flask
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-world-flask
  template:
    metadata:
      labels:
        app: hello-world-flask
    spec:
      containers:
      - image: lyzhang1999/hello-world-flask:latest
        name: hello-world-flask
```

```
$ mkdir fluxcd-demo && cd fluxcd-demo
$ git init
$ git add -A && git commit -m "Add deployment"
$ git branch -M main$ git remote add origin https://github.com/lyzhang1999/fluxcd-demo.git$ git push -u origin main
```

#### 为 FluxCD 创建仓库连接信息

fluxcd-repo.yaml

```
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: GitRepository
metadata:
  name: hello-world-flask
spec:
  interval: 5s
  ref:
    branch: main
  url: https://github.com/lyzhang1999/fluxcd-demo
```

```
$ kubectl apply -f fluxcd-repo.yaml
$ kubectl get gitrepository
```

#### 为 FluxCD 创建部署策略

fluxcd-kustomize.yaml

```
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: hello-world-flask
spec:
  interval: 5s
  path: ./
  prune: true
  sourceRef:
    kind: GitRepository
    name: hello-world-flask
  targetNamespace: default
```

```
$ kubectl apply -f fluxcd-kustomize.yaml
$ kubectl get kustomization
```

#### 自动发布

修改 fluxcd-demo 仓库的 deployment.yaml 文件，将 image 字段的镜像版本从 latest 修改为 v1，然后将修改推送到远端仓库

```
$ git add -A && git commit -m "Update image tag to v1"
$ git push origin main
```

查看触发重新部署的事件

```
kubectl describe kustomization hello-world-flask
```

#### 发布回滚

使用 git log 找到上一次的提交记录的 commit id，使用 git reset 来回滚到上一次提交，并强制推送到 Git 仓库。

```
$ git log
$ git reset --hard 75f39dc
$ git push origin main -f
```

查看触发重新部署的事件

```
kubectl describe kustomization hello-world-flask
```

> 此文章为 4 月 Day20 学习笔记，内容来源于极客时间[云原生架构与 GitOps 实战](http://gk.link/a/121Vm)，强烈推荐该课程！
