# Google BBR 拥塞控制算法原理

### BBR 在 Youtube 上的应用

* 吞吐量提升
* RTT 时延变短
* 重新缓冲时间间隔变长

### 最佳控制点在哪？ --1979 Leonard Kleinrock

* 基于丢包的拥塞控制算法
  - 高时延，大量丢包
  - 随着内存便宜，时延更高
* 左边纵线（对整体网络有效）
  - 最大带宽下
  - 最小时延
  - 最低丢包率
* RTprop 与 BtlBw 独立变化
  - 同时只有一个可以被准确测量

### BBR 如何找到准确的 RTprop 和 BtlBw？

* RTT 里有排队噪声
  - ACK 延迟确认、网络设备排队
* 什么是 RTprop？是物理属性
* 如何测量出 RTprop？
* 如何测量出 BtlBw？

### 基于 pacing_gain 调整

* 700 ms内的测量
  - 10-Mbps, 40-ms链路
* 如何检测带宽变大？
  - 定期提升pacing_gain

### Google B4 WAN实践

* 2-25 倍吞吐量提升
* 75% 连接受限于 linux kerner 接收缓存
* 在美国-欧洲路径上提升 linux kernal 接收缓存上限后有 133 倍提升

> 此文章为 4 月 Day1 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
