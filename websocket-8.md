# 如何使用 Wireshark 解密 TLS/SSL 报文

### Chrome 浏览器检测 HTTP/2 插件

[HTTP Indicator](https://github.com/pd4d10/http-indicator)
* <https://chrome.google.com/webstore/detail/http-indicator/hgcomhbcacfkpffiphlmnlhpppcjgmbl>

### 在 HTTP/2 应用层协议之下的 TLS 层

### TLS1.2 的加密算法

* 常见加密套件
* 对称加密算法：AES_128_GCM
* 每次建立连接后，加密密钥都不一样
* 密钥生成算法：ECDHE
* 客户端与服务器通过交换部分信息，各自独立生成最终一致的密钥

### Wireshark 如何解密 TLS 消息？

* 原理：获得 TLS 握手阶段生成的密钥
  - 通过 Chrome 浏览器 DEBUG 日志中的握手信息生成密钥
* 步骤
  - 配置 Chrome 输出 DEBUG 日志
    - 配置环境变量 SSLKEYLOGFILE
  - 在 Wireshark 中配置解析 DEBUG 日志
    - 编辑->首选项->Protocols->TLS/SSL
      - (Pre)-Master-Secret log filename

```
open -n "/Applications/Google Chrome.app" --args --user-data-dir=/tmp/chrome --ssl-key-log-file=/tmp/chrome/ssl-key.log
```

> [MacOS 下 Wireshark 抓取 Chrome HTTPS](https://segmentfault.com/a/1190000021142289)

> 此文章为 2 月 Day8 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
