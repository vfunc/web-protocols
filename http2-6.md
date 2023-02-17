# 帧格式：帧类型及设置帧的子类型

### 9 字节标准帧头部：帧长度

* 0 至 2<sup>14</sup> (16,384) -1
  - 所有实现必须可以支持 16KB 以下的帧
* 2<sup>14</sup> (16,384) 至 2<sup>24</sup>-1 (16,777,215)
  - 传递 16KB 到 16MB 的帧时，必须接收端首先公布自己可以处理此大小
    - 通过 SETTINGS_MAX_FRAME_SIZE 帧（Identifier=5）告知

### 帧类型

| 帧类型 | 类型编码 | 用途                  |
|---|---|---------------------|
| DATA | 0x0 | 传递HTTP包体            |
| HEADERS | 0x1 | 传递HTTP头部            |
| PRIORITY | 0x2 | 指定Stream流的优先级       |
| RST_STREAM | 0x3 | 终止Stream流           |
| SETTINGS | 0x4 | 修改连接或者Stream流的配置    |
| PUSH_PROMISE | 0x5 | 服务端推送资源时描述请求的帧      |
| PING | 0x6 | 心跳检测，兼具计算RTT往返时间的功能 |
| GOAWAY | 0x7 | 优雅的终止连接或者通知错误       |
| WINDOW_UPDATE | 0x8 | 实现流量控制              |
|CONTINUATION | 0x9 | 传递较大HTTP头部时的持续帧     |

### Setting 设置帧格式（type=0x4）

* 设置帧并不是“协商”，而是发送方向接收方通知其特性、能力
* 一个设置帧可同时设置多个对象
  - Identifier：设置对象
  - Value：设置值

### Setting 设置对象的类型

* SETTINGS_HEADER_TABLE_SIZE (0x1): 通知对端索引表的最大尺寸（单位字节，初始 4096 字节）
* SETTINGS_ENABLE_PUSH (0x2): Value设置为 0 时可禁用服务器推送功能，1 表示启用推送功能
* SETTINGS_MAX_CONCURRENT_STREAMS (0x3): 告诉接收端允许的最大并发流数量
* SETTINGS_INITIAL_WINDOW_SIZE (0x4): 声明发送端的窗口大小，用于Stream级别流控，初始值2^16-1 (65,535) 字节
* SETTINGS_MAX_FRAME_SIZE (0x5):设置帧的最大大小，初始值 2^14 (16,384)字节
* SETTINGS_MAX_HEADER_LIST_SIZE (0x6): 知会对端头部索引表的最大尺寸，单位字节，基于未压缩前的头部

> 此文章为 2 月 Day12 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！

---

另外，最近重温操作系统时发现了一个免费精品好课，闪客的《Linux0.11源码趣读》，这个课给我感觉像在用看小说的心态学操作系统源码，写的确实挺牛的，通俗易懂，直指本源，我自己也跟着收获了很多。这个课在极客时间上是免费的，口碑很不错，看评论下很多人在催更和重温，强烈推荐！[戳此链接领取](https://time.geekbang.org/opencourse/intro/100310101?utm_source=linux_dk&utm_term=linux_dk)
