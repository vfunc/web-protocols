# 四次握手关闭连接

### 关闭连接：防止数据丢失

* FIN：结束
* ACK：确认

### TCP 状态机

* 11 种状态
  - CLOSED
  - LISTEN
  - SYN-SENT
  - SYN-RECEIVED
  - ESTABLISHED
  - CLOSE-WAIT
  - LAST-ACK
  - FIN-WAIT1
  - FIN-WAIT2
  - CLOSING
  - TIME-WAIT
* 3 种事件
  - SYN
  - FIN
  - ACK

### TIME-WAIT状态过短或者不存在会怎么样？

* MSL(Maximum Segment Lifetime)
  - 报文最大生存时间
* 维持 2MSL 时长的 TIME-WAIT 状态
  - 保证至少一次报文的往返时间内 端口是不可复用

### linux下TIME_WAIT优化

* net.ipv4.tcp_tw_reuse = 1
  - 开启后，作为客户端时新连接可 以使用仍然处于 TIME-WAIT 状 态的端口
  - 由于 timestamp 的存在，操作系 统可以拒绝迟到的报文
    - net.ipv4.tcp_timestamps = 1
* net.ipv4.tcp_tw_recycle = 0
  - 开启后，同时作为客户端和服务器都可以使用 TIME-WAIT 状态的端口
  - 不安全，无法避免报文延迟、重复等给新连接造成混乱
* net.ipv4.tcp_max_tw_buckets = 262144
  - time_wait 状态连接的最大数量
  - 超出后直接关闭连接

> 此文章为 4 月 Day2 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
