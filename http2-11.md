# RST_STREAM 帧及常见错误码

### RST_STREAM 帧（type=0x3）

* HTTP2 多个流共享同一连接，RST 帧允许立刻终止一个未完成的流
* RST_STRAM 帧不使用任何 flag
* RST_STREAM 帧的格式 `Error Code (32)`

### 常见错误码

* NO_ERROR (0x0): 没有错误。GOAWAY帧优雅关闭连接时可以使用此错误码
* PROTOCOL_ERROR (0x1): 检测到不识别的协议字段
* INTERNAL_ERROR (0x2):内部错误
* FLOW_CONTROL_ERROR (0x3): 检测到对端没有遵守流控策略
* SETTINGS_TIMEOUT (0x4): 某些设置帧发出后需要接收端应答，在期待时间 内没有得到应答则由此错误码表示
* STREAM_CLOSED (0x5): 当Stream已经处于半关闭状态不再接收Frame帧时， 又接收到了新的Frame帧
* FRAME_SIZE_ERROR (0x6): 接收到的Frame Size不合法
* REFUSED_STREAM (0x7): 拒绝先前的Stream流的执行
* CANCEL (0x8): 表示Stream不再存在
* COMPRESSION_ERROR (0x9): 对HPACK压缩算法执行失败
* CONNECT_ERROR (0xa): 连接失败
* ENHANCE_YOUR_CALM (0xb): 检测到对端的行为可能导致负载的持续增加， 提醒对方“冷静”一点
* INADEQUATE_SECURITY (0xc): 安全等级不够
* HTTP_1_1_REQUIRED (0xd): 对端只能接受HTTP/1.1协议

> 此文章为 3 月 Day1 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
