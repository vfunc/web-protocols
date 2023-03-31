# 从丢包到测量驱动的拥塞控制算法

### 飞行中的数据与确认报文

### 大管道向小管道传输数据引发拥堵

### 最佳控制点在哪？ --1979 Leonard Kleinrock

* 基于丢包的拥塞控制点
  - 高时延，大量丢包
  - 随着内存便宜，时延更高
* 最佳控制点
  - 最大带宽下
  - 最小时延
  - 最低丢包率
* RTT 与 Bw 独立变化
  - 同时只有一个可以被准确测量

### 空队列的效果最好！

### BBR：TCP Bottleneck Bandwidth and Round-trip propagation time

* 由 Google 于 2016 发布，Linux4.9 内核引入，QUIC 使用

> 此文章为 3 月 Day31 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
