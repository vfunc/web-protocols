# 基于 openssl 实战验证 RSA

RSA算法是一个广泛使用的非对称加密算法。其密钥包括公钥和私钥。它能用于数字签名、身份认证以及密钥交换。RSA密钥长度一般使用1024位或者更高。

### 使用 openssl 基于 RSA 算法生成公私钥

* 生成私钥（公私钥格式参见 RFC3447）
  - openssl genrsa -out private.pem
* 从私钥中提取出公钥
  - openssl rsa -in private.pem -pubout -out public.pem
* 查看 ASN.1 格式的私钥
  - openssl asn1parse -i -in private.pem
* 查看 ASN.1 格式的公钥
  - openssl asn1parse -i -in public.pem (X.590)
  - openssl asn1parse -i -in public.pem -strparse 19

### 使用 RSA 公私钥加解密

* 加密文件
  - openssl rsautl -encrypt -in hello.txt -inkey public.pem -pubin -out hello.en
* 解密文件
  - openssl rsautl -decrypt -in hello.en -inkey private.pem -out hello.de

> 此文章为 3 月 Day11 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
