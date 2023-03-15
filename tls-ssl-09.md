# 基于椭圆曲线的 ECDH 协议

### ECDH 密钥交换协议

* DH 密钥交换协议使用椭圆曲线后的变种，称为 Elliptic Curve Diffie–Hellman key Exchange，缩写为 ECDH，优点是比 DH 计算速度快、同等安全条件下密钥更短
* ECC（Elliptic Curve Cryptography）：椭圆曲线密码学
* 魏尔斯特拉斯椭圆函数（Weierstrass‘s elliptic functions）： $y^2$ = $x^3$ + ax + b

### ECC 的关键原理

* Q=K.P
* 已知 K 与 P，正向运算快速
* 已知 Q 与 P，计算 K 的逆向运算非常困难

### ECDH 的步骤

* 步骤
  - Alice 选定大整数 Ka 作为私钥
  - 基于选定曲线及曲线上的共享 P 点，Alice 计算出 Qa=Ka.P
  - Alice 将 Qa、选定曲线、共享 P 点传递点 Bob
  - Bob 选定大整数 Kb 作为私钥，将计算了 Qb=Kb.P，并将 Qb 传递给 Alice
  - Alice 生成密钥 Qb.Ka = (X, Y)，其中 X 为对称加密的密钥
  - Bob 生成密钥 Qa.Kb = (X, Y)，其中 X 为对称加密的密钥
* Qb.Ka = Ka.(Kb.P) = Ka.Kb.P = Kb.(Ka.P) = Qa.Kb

### X25519 曲线
* 椭圆曲线变种：Montgomery curve 蒙哥马利曲线
  - B $y^2$ = $x^3$ + A $x^2$ + x
* X25519： $y^2$ = $x^3$ + 486662 $x^2$ + x
  - p 等于 $2^255$ – 19，基点 G=9
    - order N
    - $2^252$ + 0x14def9dea2f79cd65812631a5cf5d3ed

> 此文章为 3 月 Day15 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
