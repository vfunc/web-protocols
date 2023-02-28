# stream 的状态变迁

### Stream 特性

* 一条 TCP 连接上，可以并发存在多个处于 OPEN 状态的 Stream
* 客户端或者服务器都可以创建新的 Stream
* 客户端或者服务器都可以首先关闭 Stream
* 同一条 Stream 内的 Frame 帧是有序的
* 从 Stream ID 的值可以轻易分辨 PUSH 消息
  - 所有为发送 HEADER/DATA 消息而创建的流，从1、3、5 等递增奇数开始
  - 所有为发送 PUSH 消息而创建的流，从 2、4、6 等递增偶数开始

### Message 特性

* 一条 HTTP Message 由 1 个 HEADER（可能含有 0 个或者多个持续帧构成） 及 0 个或者多个 DATA 帧构成
* HEADER 消息同时包含 HTTP/1.1 中的 start line 与 headers 部分
* 取消 HTTP/1.1 中的不定长 Chunk 消息

### GET 消息发送示例

客户端

```
GET / HTTP/1.1
Host: svrdomain
```

服务器

```
HTTP/1.1 200 OK
Content-Length: 10

HelloWorld
```

### POST 消息发送示例

客户端

```
POST / HTTP/1.1
Host: svrdomain
Content-Length: 7

a=1&b=2
```
服务器

```
HTTP/1.1 200 OK
Content-Length: 10

HelloWorld
```

### Stream 流的状态

* 帧符号
  - H: HEADERS 帧
  - PP: PUSH_PROMISE 帧
  - ES: END_STREAM 标志位
  - R: RST_STREAM 帧
* 流状态
  - idle：起始状态
  - closed
  - open：可以发送任何帧
  - half closed 单向关闭
    - remote：不再接收数据帧
    - local：不能再发送数据帧
  - reserved
    - remote
    - local

> 此文章为 2 月 Day16 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
