# 详解 AES 对称加密算法

### AES（Advanced Encryption Standard）加密算法

* 为比利时密码学家 Joan Daemen 和 Vincent Rijmen 所设计，又称 Rijndael 加密算法
* 常用填充算法：PKCS7
* 常用分组工作模式：GCM

### AES 的三种密钥长度

* AES分组长度是 128 位（16 字节）

| AES      | 密钥长度（32 位比特) | 分组长度(32 位比特) | 加密轮数 |
|----------|-------------------|-------------------|---------|
| AES-128  | 4                 | 4                 |      10 |
| AES-192  | 6                 | 4                 |      12 |
| AES-256  | 8                 | 4                 |      14 |

### AES 的加密步骤

* 把明文按照 128bit（16 字节）拆分成若干个明文块，每个明文块是 4*4 矩阵
* 按照选择的填充方式来填充最后一个明文块
* 每一个明文块利用 AES 加密器和密钥，加密成密文块
* 拼接所有的密文块，成为最终的密文结果

### AES 加密流程

* C = E(K,P)，E 为每一轮算法，每轮密钥皆不同
  - 初始轮
    - AddRoundKey 轮密钥加
  - 普通轮
    - AddRoundKey 轮密钥加
    - SubBytes 字节替代
    - ShiftRows 行移位
    - MixColumns 列混合
  - 最终轮
    - SubBytes 字节替代
    - ShiftRows 行移位
    - AddRoundKey 轮密钥加

> 此文章为 3 月 Day9 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
