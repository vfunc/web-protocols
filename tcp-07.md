# 重传与确认

### 报文有可能丢失

### PAR：Positive Acknowledgment with Retransmission

### 提升并发能力的 PAR 改进版

* 接收缓冲区的管理
  - Limit 限制发送方

### Sequence 序列号/Ack 序列号

* 设计目的：解决应用层字节流的可靠发送
  - 跟踪应用层的发送端数据是否送达
  - 确定接收端有序的接收到字节流
* 序列号的值针对的是字节而不是报文

### 确认序号

### TCP 序列号

### PAWS (Protect Against Wrapped Sequence numbers)

* 防止序列号回绕

### BDP 网络中的问题

* TCP timestamp
  - 更精准的计算 RTO
  - PAWS

> 此文章为 3 月 Day23 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
