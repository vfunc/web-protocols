# 《Web 协议详解与抓包实战》学习笔记 Day 10

## Cookie 是什么？

RFC6265, HTTP State Management Mechanism

保存在客户端、由浏览器维护、表示应 用状态的 HTTP 头部

* 存放在内存或者磁盘中
* 服务器端生成 Cookie 在响应中通过 Set-Cookie 头部告知客户端（允许多 个 Set-Cookie 头部传递多个值）
* 客户端得到 Cookie 后，后续请求都会 自动将 Cookie 头部携带至请求中

## Cookie 与 Set-Cookie 头部的定义

* Cookie 头部中可以存放多个 name/value 名值对
  - cookie-header = "Cookie:" OWS cookie-string OWS
    - cookie-string = cookie-pair *( ";" SP cookie-pair )
      - cookie-pair = cookie-name "=" cookie-value
* Set-Cookie 头部一次只能传递 1 个 name/value 名值对，响应中可以含多个头部
  - set-cookie-header = "Set-Cookie:" SP set-cookie-string
    - set-cookie-string = cookie-pair *( ";" SP cookie-av )
      - cookie-pair = cookie-name "=" cookie-value
      - cookie-av：描述 cookie-pair 的可选属性

## Set-Cookie 中描述 cookie-pair 的属性

cookie-av = expires-av / max-age-av / domain-av / path-av / secure-av / httponly-av / extension-av

* expires-av = "Expires=" sane-cookie-date
  - cookie 到日期 sane-cookie-date 后失效
* max-age-av = "Max-Age=" non-zero-digit *DIGIT
  - cookie 经过 *DIGIT 秒后失效。max-age 优先级高于 expires
* domain-av = "Domain=" domain-value
  - 指定 cookie 可用于哪些域名，默认可以访问当前域名
* path-av = "Path=" path-value
  - 指定 Path 路径下才能使用 cookie
* secure-av = "Secure“
  - 只有使用 TLS/SSL 协议（https）时才能使用 cookie
* httponly-av = "HttpOnly“
  - 不能使用 JavaScript（Document.cookie 、XMLHttpRequest 、Request APIs）访问到 cookie

## Cookie 使用的限制

* RFC 规范对浏览器使用 Cookie 的要求
  - 每条 Cookie 的长度（包括 name、value 以及描述的属性等总长度）至于要达到 4KB
  - 每个域名下至少支持 50 个 Cookie
  - 至少要支持 3000 个 Cookie
* 代理服务器传递 Cookie 时会有限制

## Cookie 在协议设计上的问题

* Cookie 会被附加在每个 HTTP 请求中，所以无形中增加了流量
* 由于在 HTTP 请求中的 Cookie 是明文传递的，所以安全性成问题（除 非用 HTTPS）
* Cookie 的大小不应超过 4KB，故对于复杂的存储需求来说是不够用的

## 登录场景下 Cookie 与 Session 的常见用法
![image.png](img/day10/01.png)

## 无状态的 REST 架构 VS 状态管理

* 应用状态与资源状态
  - 应用状态：应由客户端管理，不应由服务器管理
    - 如浏览器目前在哪一页
    - REST 架构要求服务器不应保存应用状态
  - 资源状态：应由服务器管理，不应由客户端管理
    - 如数据库中存放的数据状态，例如用户的登陆信息
* HTTP 请求的状态
  - 有状态的请求：服务器端保存请求的相关信息，每个请求可以使用以前保留的请求相关信息
    - 服务器 session 机制使服务器保存请求的相关信息
    - cookie 使请求可以携带查询信息，与 session 配合完成有状态的请求
  - 无状态的请求：服务器能够处理的所有信息都来自当前请求所携带的信息
    - 服务器不会保存 session 信息
    - 请求可以通过 cookie 携带

## 第三方 Cookie

浏览器允许对于不安全域下的资源（如广告图片）响应中的Set-Cookie保存，并在后续访问该域时自动使用 Cookie

* 用户踪迹信息的搜集

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
