# 不同于 TCP 的流量控制

### 为什么需要 HTTP/2 应用层流控

* HTTP/1.1 中由 TCP 层进行流量控制
  - 前提：HTTP/1 的 TCP 连接上没有多路复用
* HTTP/2 中，多路复用意味着多个 Stream 必须共享 TCP 层的流量控制
  - 问题：多 Stream 争夺 TCP 的流控制，互相干扰可能造成 Stream 阻塞

 ### 由应用层决定发送速度

* HTTP/2 中的流控制既针对单个 Stream，也针对整个 TCP 连接
  - 客户端与服务器都具备流量控制能力
  - 单向流控制：发送和接收独立设定流量控制
  - 以信用为基础：接收端设定上限，发送端应当遵循接收端发出的指令
  - 流量控制窗口（流或者连接）的初始值是 65535 字节
  - 只有 DATA 帧服从流量控制
  - 流量控制不能被禁用

 ### WINDOW_UPDATE 帧

* type=0x8，不使用任何 flag
* 窗口范围 1 to 231-1 (2,147,483,647)字节
  - 0 是错误的，接收端应返回 PROTOCOL_ERROR
* 当 Stream ID 为 0 时表示对连接流控，否则为对 Stream 流控
* 流控仅针对直接建立 TCP 连接的两端
  - 代理服务器并不需要透传 WINDOW_UPDATE 帧
    - 接收端的缩小流控窗口会最终传递到源发送端

 ### 流控制窗口

* 窗口大小由接收端告知
* 窗口随着 DATA 帧的发送而减少

 ### SETTINGS_MAX_CONCURRENT_STREAMS 并发流
* 并发仅统计 open 或者 half-close 状态的流（不包含用于推送的 reserved 状态）
* 超出限制后的错误码
  - PROTOCOL_ERROR
  - REFUSED_STREAM

> 此文章为 3 月 Day3 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
