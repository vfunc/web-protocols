# stream 的优先级

## 不同请求的优先级

### Priority 优先级设置帧

* 帧类型：type=0x2
* 不使用 flag 标志位字段
* Stream Dependency：依赖流
* Weight权重：取值范围为 1 到 256。默认权重16
* 仅针对 Stream 流，若 ID 为 0 试图影响连接，则接收端必须报错
* 在 idle 和 closed 状态下，仍然可以发送 Priority 帧

### 数据流优先级

* 每个数据流有优先级（1-256）
* 数据流间可以有依赖关系

### exclusive 标志位

* E 设为 0
* E 设为 1

> 此文章为 3 月 Day2 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
