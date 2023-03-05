# HTTP/2 的问题及 HTTP/3 的意义

### TCP 以及 TCP+TLS 建链握手过多的问题

### 多路复用与 TCP 的队头阻塞问题

### TCP的问题

* 由操作系统内核实现，更新缓慢

### QUIC 协议在哪一层？

### 使 Chrome 支持 QUIC

* chrome://flags/#enable-quic

### QUIC 协议概述

* HTTP/3 与 QUIC 协议
* HTTP3 的连接迁移
  - 允许客户端更换 IP 地址、端口后，仍然可以复用前连接
* 解决了队头阻塞问题的 HTTP3
  - UDP 报文：先天没有队列概念
* HTTP3：1RTT 完全握手
* 会话恢复场景下的 0RTT 握手
* HTTP3：0RTT 恢复会话握手

> 此文章为 3 月 Day5 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
