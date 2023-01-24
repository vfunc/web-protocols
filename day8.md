# 《Web 协议详解与抓包实战》学习笔记 Day 8

## HTTP 包体：承载的消息内容

* 请求或者响应都可以携带包体
  - HTTP-message = start-line *( header-field CRLF ) CRLF [ message-body ]
  - message-body = *OCTET：二进制字节流
* 以下消息不能含有包体
  - HEAD 方法请求对应的响应
  - 1xx、204、304 对应的响应
  - CONNECT 方法对应的 2xx 响应

## 两种传输 HTTP 包体的方式

* 发送 HTTP 消息时已能够确定包体的全部长度
  - 使用 Content-Length 头部明确指明包体长度
  - Content-Length = 1*DIGIT
  - 用 10 进制（不是 16 进制）表示包体中的字节个数，且必须与实际传输的包体长度一致
  - 优点：接收端处理更简单
* 发送 HTTP 消息时不能确定包体的全部长度
  - 使用 Transfer-Encoding 头部指明使用 Chunk 传输方式
  - 含 Transfer-Encoding 头部后 Content-Length 头部应被忽略
  - 优点
    - 基于长连接持续推送动态内容
    - 压缩体积较大的包体时，不必完全压缩完（计算出头部）再发送，可以边发送边压缩
    - 传递必须在包体传输完才能计算出的 Trailer 头部
* 不定长包体的 chunk 传输方式
  - Transfer-Encoding头部
    - transfer-coding = "chunked" / "compress" / "deflate" / "gzip" / transfer-extension
    - Chunked transfer encoding 分块传输编码： Transfer-Encoding：chunked
    - chunked-body = *chunk last-chunk trailer-part CRLF
    - chunk = chunk-size [ chunk-ext ] CRLF chunk-data CRLF
      - chunk-size = 1*HEXDIG：注意这里是 16 进制而不是10进制
      - chunk-data = 1*OCTET
    - last-chunk = 1*("0") [ chunk-ext ] CRLF
    - trailer-part = *( header-field CRLF )
  - Trailer 头部的传输
    - TE 头部：客户端在请求在声明是否接收 Trailer 头部
      - TE: trailers
    - Trailer 头部：服务器告知接下来 chunk 包体后会传输哪些 Trailer 头部
      - Trailer: Date
    - 以下头部不允许出现在 Trailer 的值中：
      - 用于信息分帧的首部 (例如 Transfer-Encoding 和 Content-Length)
      - 用于路由用途的首部 (例如 Host)
      - 请求修饰首部 (例如控制类和条件类的，如 Cache-Control，Max-Forwards，或者 TE)
      - 身份验证首部 (例如 Authorization 或者 Set-Cookie)
      - Content-Encoding, Content-Type, Content-Range，以及 Trailer 自身

* MIME
  - MIME（ Multipurpose Internet Mail Extensions ）
  - content := "Content-Type" ":" type "/" subtype *(";" parameter)
    - type := discrete-type / composite-type
      - discrete-type := "text" / "image" / "audio" / "video" / "application" / extension-token
      - composite-type := "message" / "multipart" / extension-token
      - extension-token := ietf-token / x-token
    - subtype := extension-token / iana-token
    - parameter := attribute "=" value
  - 大小写不敏感，但通常是小写
  - 例如： Content-type: text/plain; charset="us-ascii"
  - https://www.iana.org/assignments/media-types/media-types.xhtml

* Content-Disposition 头部(RFC6266)
  - disposition-type = "inline" | "attachment" | disp-ext-type
  - inline：指定包体是以 inline 内联的方式，作为页面的一部分展示
  - attachment：指定浏览器将包体以附件的方式下载
    - 例如： Content-Disposition: attachment
    - 例如： Content-Disposition: attachment; filename="filename.jpg"
  - 在 multipart/form-data 类型应答中，可以用于子消息体部分
    - 如 Content-Disposition: form-data; name="fieldName"; filename="filename.jpg"

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

# filename: testsvr.py

import socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_address = ("127.0.0.1", 12345)
sock.bind(server_address)
sock.listen(100)

while True:
    conn, client_address = sock.accept()
    try:
        data = conn.recv(4096)
        response = 'HTTP/1.1 200 OK\r\nContent-Length: 5\r\n\r\nHello'
        conn.send(response.encode())
    finally:
        conn.close()
```

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
