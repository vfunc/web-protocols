# K8s如何实现自动自愈和自动扩容

### 自愈解决什么问题？

* 服务自动重启，当业务进程意外中断，或者节点产生故障时，系统可以快速识别，自动重启并恢复服务。
* 自动转移故障，让业务不健康的节点不接收流量，保证用户体验。

### K8s 的自动自愈

```
$ kubectl create deployment hello-world-flask --image=lyzhang1999/hello-world-flask:latest --replicas=2
```

* hello-world-flask 代表工作负载的名称
* -image 代表镜像
* –replicas 指的是 Pod 副本数

这条命令会生成 Deployment Manifest，然后自动执行 kubectl apply 将 Manifest 应用到集群内，省略了我们手动编写 Manifest 的过程。
可以为上面的命令增加 --dry-run 和 -o 参数，单纯输出 Manifest 内容。

```
$ kubectl create deployment hello-world-flask --image lyzhang1999/hello-world-flask:latest --replicas=2 --dry-run=client -o yaml
```

创建 Service

```
$ kubectl create service clusterip hello-world-flask --tcp=5000:5000
```

创建 Ingress

```
$ kubectl create ingress hello-world-flask --rule="/=hello-world-flask:5000"
```

部署 Ingress-nginx

```
$ kubectl create -f https://ghproxy.com/https://raw.githubusercontent.com/lyzhang1999/resource/main/ingress-nginx/ingress-nginx.yaml
```

* Pod 会被 Deployment 工作负载管理起来，例如创建和销毁等
* Service 相当于弹性伸缩组的负载均衡器，它能以加权轮训的方式将流量转发到多个 Pod 副本上
* Ingress 相当于集群的外网访问入口

列出 Pod

```
$ kubectl get pods
```

每隔 1 秒钟发送一次请求

```
$ while true; do sleep 1; curl http://127.0.0.1; echo -e '\n'$(date);done
```

模拟其中的一个 Pod 宕机

```
$ kubectl exec -it hello-world-flask-56fbff68c8-2xz7w -- bash -c "killall python3"
```

* 查看请求窗口，看到请求流量都被转发到了没有故障的 Pod，故障成功地被转移了！
* 等待几秒钟，继续观察，Pod 被重启恢复后，重新加入到了负载均衡接收外部流量。
* 再次执行 kubectl get pods 查看 Pod 发现 RESTARTS 值为 1，也就是说 K8s 自动帮我们重启了这个 Pod。

K8s 的自愈功能：首先， K8s 感知到了业务 Pod 故障，立刻进行了故障转移并隔离了有故障的 Pod，并将请求转发到了其他健康的 Pod 中。随后重启了有故障的 Pod，最后将重启后的 Pod 加入到了负载均衡并开始接收外部请求。

### K8s 的自动扩容

自动扩容依赖于 K8s Metric Server 提供的监控指标，先安装

```
$ kubectl apply -f https://ghproxy.com/https://raw.githubusercontent.com/lyzhang1999/resource/main/metrics/metrics.yaml
```

安装完成后，等待 Metric 工作负载就绪

```
$ kubectl wait deployment -n kube-system metrics-server --for condition=Available=True --timeout=90s
```

Metric Server 就绪后，为 Deployment 创建自动扩容策略

```
$ kubectl autoscale deployment hello-world-flask --cpu-percent=50 --min=2 --max=10
```

* –cpu-percent 表示 CPU 使用率阈值，当 CPU 超过 50% 时将进行自动扩容
* –min 代表最小扩容的 Pod 副本数
* –max 代表最大扩容的 Pod 副本数

设置资源配额

```
$ kubectl patch deployment hello-world-flask --type='json' -p='[{"op": "add", "path": "/spec/template/spec/containers/0/resources", "value": {"requests": {"memory": "100Mi", "cpu": "100m"}}}]'
```

Deployment 将会重新创建两个新的 Pod

```
$ kubectl get pod --field-selector=status.phase==Running
```

选择一个 Pod 并进入到容器内

```
$ kubectl exec -it hello-world-flask-64dd645c57-4clbp -- bash
```

模拟业务高峰期场景

```
root@hello-world-flask-64dd645c57-4clbp:/app# ab -c 50 -n 10000 http://127.0.0.1:5000/
```

* -c 代表 50 个并发数
* -n 代表一共请求 10000 次

新开一个命令行窗口，持续监控 Pod 的状态

```
$ kubectl get pods --watch
```

* --watch 表示持续监听 Pod 状态变化

在 ab 压力测试的过程中，会不断创建新的 Pod 副本，这说明 K8s 已经感知到了 Pod 的业务压力，并且正在自动进行横向扩容。

K8s 的自愈和扩容的对象都是 Pod，它是 K8s 的最小调度单位。通过创建 Deployment 工作负载来间接创建 Pod，可以很方便地创建多个 Pod 副本，并且只需要关注 Deployment 的状态就可以间接地控制 Pod 的状态。

> 此文章为 4 月 Day19 学习笔记，内容来源于极客时间[云原生架构与 GitOps 实战](http://gk.link/a/121Vm)，强烈推荐该课程！
