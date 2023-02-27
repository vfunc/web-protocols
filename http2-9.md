# 服务器端的主动消息推送

### 服务器推送的价值

* 提前将资源推送至浏览器缓存
* 特性
  - 推送可以基于已经发送的请求，例如客户端请求 html，主动推送 js 文件
* 实现方式
  - 推送资源必须对应一个请求
  - 请求由服务器端PUSH_PROMISE 帧发送
  - 响应在偶数 ID 的 STREAM 中发送

### 当获取 HTML 后，需要 CSS 资源时

* 浏览器触发方式：需要两次往返！
* PUSH_PROMISE 方式
  - 在 Stream1 中通知客户端 CSS 资源即将来临
  - 在 Stream2 中发送 CSS 资源（Stream1 和 2 可以并发！）

### 服务器推送 PUSH

* PUSH 帧的格式
  - PUSH_PROMISE 帧，type=0x5，只能由服务器发送
* PUSH 推送模式的禁用
  - SETTINGS_ENABLE_PUSH（0x2）
    - 1表示启用推送功能
    - 0表示禁用推送功能

> 此文章为 2 月 Day15 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
