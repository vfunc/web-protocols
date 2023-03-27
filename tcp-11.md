# 如何减少小报文提高网络效率

### SWS(Silly Window syndrome)糊涂窗口综合症

* 小窗口通告

### SWS 避免算法

* 接收方
  - David D Clark 算法：窗口边界移动值小于 min（MSS, 缓存/2）时，通知窗口为 0
* 发送方
  - Nagle 算法：TCP_NODELAY 用于关闭 Nagle 算法
    - 没有已发送未确认报文段时，立刻发送数据
    - 存在未确认报文段时，直到：1-没有已发送未确认报文段，或者 2-数据长度达到 MSS 时再发送

### TCP delayed acknowledgment 延迟确认

* 当有响应数据要发送时, ack 会随着响应数据立即发送给对方
* 如果没有响应数据, ack 的发 送将会有一个延迟,以等待看是否有响应数据可以一起发送
* 如果在等待发送 ack 期间,对方的第二个数据段又到达了,这时要立即发送 ack

### Nagle VS delayed ACK

* 关闭 delayed ACK：TCP_QUICKACK
* 关闭 Nagle：TCP_NODELAY

### Linux 上更为激进的“Nagle”：TCP_CORK

* 结合 sendfile 零拷贝技术使用

> 此文章为 3 月 Day27 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
