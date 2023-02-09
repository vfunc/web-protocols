# 如何从 HTTP 升级到 WebSocket

### URI 格式

* ws-URI = "ws:" "//" host [ ":" port ] path [ "?" query ]
  - 默认 port 端口 80
* wss-URI = "wss:" "//" host [ ":" port ] path [ "?" query ]
  - 默认 port 端口 443
* 客户端提供信息
  - host 与 port：主机名与端口
  - shema：是否基于 SSL
  - 访问资源：URI
  - 握手随机数：Sec-WebSocket-Key
  - 选择子协议： Sec-WebSocket-Protocol
  - 扩展协议： Sec-WebSocket-Extensions
  - CORS 跨域：Origin

### 建立握手

* 客户端请求

```
GET wss://socketsbay.com/wss/v2/1/demo/ HTTP/1.1
Host: socketsbay.com
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: https://socketsbay.com
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: gOcqPMiNFMqOEFHMxdU6DA==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

* 服务器响应

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: 2dkQdHYzFfx95Kcq7L92Dxn4qII=
```


### 如何证明握手被服务器接受？预防意外

* 请求中的 Sec-WebSocket-Key 随机数
  - 例如 Sec-WebSocket-Key: A1EEou7Nnq6+BBZoAZqWlg==
* 响应中的 Sec-WebSocket-Accept 证明值
  - GUID（RFC4122）：258EAFA5-E914-47DA-95CA-C5AB0DC85B11
  - 值构造规则：BASE64(SHA1(Sec-WebSocket-KeyGUID))
    - 拼接值：A1EEou7Nnq6+BBZoAZqWlg==258EAFA5-E914-47DA-95CA-C5AB0DC85B11
    - SHA1 值：713f15ece2218612fcadb1598281a35380d1790f
    - BASE 64 值：cT8V7OIhhhL8rbFZgoGjU4DReQ8=
    - 最终头部：Sec-WebSocket-Accept: cT8V7OIhhhL8rbFZgoGjU4DReQ8=

> 此文章为 2 月 Day4 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
