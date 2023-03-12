# 非对称密码应用：PKI 证书体系

### 非对称密码应用：数字签名

* 基于私钥加密，只能使用公钥解密：起到身份认证的使用
* 公钥的管理：Public Key Infrastructure（PKI）公钥基础设施
  - 由 Certificate Authority（CA）数字证书认证机构将用户个人身份与公开密钥关联在一起
  - 公钥数字证书组成
    - CA 信息、公钥用户信息、公钥、权威机构的签字、有效期
  - PKI 用户
    - 向 CA 注册公钥的用户
    - 希望使用已注册公钥的用户

### 签发证书流程

### 签名与验签流程

### 证书信任链

### PKI 公钥基础设施

### 证书类型

* 域名验证（domain validated， DV）证书
* 组织验证（organization validated，OV）证书
* 扩展验证（extended validation， EV）证书

### 验证证书链

> 此文章为 3 月 Day12 学习笔记，内容来源于极客时间[《Web 协议详解与抓包实战》](http://gk.link/a/11UWp)，强烈推荐该课程！
