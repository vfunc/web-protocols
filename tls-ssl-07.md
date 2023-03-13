# 非对称密码应用：DH 密钥交换协议

### RSA 密钥交换
* 由客户端生成对称加密的密钥
* 问题：没有前向保密性

### DH 密钥交换

* 1976 年由 Bailey Whitfield Diffie 和 Martin Edward Hellman 首次发表，故称为 Diffie–Hellman key exchange，简称 DH
* 它可以让双方在完全没有对方任何预先信息的条件下通过不安全信道创建起一个密钥

### DH 密钥交换协议的问题

* 中间人伪造攻击
  - 向 Alice 假装自己是 Bob，进行一次 DH 密钥交换
  - 向 Bob 假装自己是 Alice，进行一次 DH 密钥交换
* 解决中间人伪造攻击
  - 身份验证

> 此文章为 3 月 Day13 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
