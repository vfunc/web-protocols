# 操作系统缓冲区与滑动窗口的关系

### 窗口与缓存

* 应用层没有及时读取缓存

### 收缩窗口导致的丢包

* 先收缩窗口，再减少缓存
* 窗口关闭后，定时探测窗口大小

### 飞行中报文的适合数量

* bps*RTT

### Linux下调整接收窗口与应用缓存

* net.ipv4.tcp_adv_win_scale = 1
* 应用缓存 = buffer / (2^tcp_adv_win_scale)

### Linux中对TCP缓冲区的调整方式

* net.ipv4.tcp_rmem = 4096 87380 6291456
  - 读缓存最小值、默认值、最大值，单位字节，覆盖 net.core.rmem_max
* net.ipv4.tcp_wmem = 4096 16384 4194304
  - 写缓存最小值、默认值、最大值，单位字节，覆盖net.core.wmem_max
* net.ipv4.tcp_mem = 1541646 2055528 3083292
  - 系统无内存压力、启动压力模式阀值、最大值，单位为页的数量
* net.ipv4.tcp_moderate_rcvbuf = 1
  - 开启自动调整缓存模式

> 此文章为 3 月 Day26 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
