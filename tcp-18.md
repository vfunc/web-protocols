# keepalive 及多路复用

### TCP 的 Keep-Alive 功能

* Linux 的 tcp keepalive
* 发送心跳周期
* Linux: net.ipv4.tcp_keepalive_time = 7200
* 探测包发送间隔
* net.ipv4.tcp_keepalive_intvl = 75
* 探测包重试次数
* net.ipv4.tcp_keepalive_probes = 9

### 违反分层原则的校验和

* 对关键头部数据（12字节）+TCP 数据执行校验和计算

### Multiplexing 多路复用
* 在一个信道上传输多路信号或数据流的过程和技术

### HTTP2：TCP 连接之上的多路复用

* 非阻塞 socket：同时处理多个 TCP 连接
* epoll+非阻塞 socket
  - epoll 出现：linux 2.5.44
  - 进程内同时刻找到缓冲区或者连接状态变化的 所有 TCP 连接
  - 3 个 API
    - epoll_create
    - epoll_ctl
    - epoll_wait
* epoll 为什么高效？
  - 活跃连接只在总连接的一小部分
* 非阻塞+epoll+同步编程 = 协程

> 此文章为 4 月 Day3 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
