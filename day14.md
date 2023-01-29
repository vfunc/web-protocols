# 《Web 协议详解与抓包实战》学习笔记 Day 14

## 什么样的 HTTP 响应会缓存？RFC7234

* 请求方法可以被缓存理解（不只于 GET 方法）
* 响应码可以被缓存理解（404、206 也可以被缓存）
* 响应与请求的头部没有指明 no-store
* 响应中至少应含有以下头部中的 1 个或者多个：
  - Expires、max-age、s-maxage、public
  - 当响应中没有明确指示过期时间的头部时，如果响应码非常明确，也可以缓存
* 如果缓存在代理服务器上
  - 不含有 private
  - 不含有 Authorization

### 其他响应头部

* Pragma = 1#pragma-directive
  - pragma-directive = "no-cache" / extension-pragma
    - extension-pragma = token [ "=" ( token / quoted-string ) ]
  - Pragma: no-cache与Cache-Control: no-cache 意义相同

### 使用缓存作为当前请求响应的条件

* URI 是匹配的
  - URI 作为主要的缓存关键字，当一个 URI 同时对应多份缓存时，选择日期最 近的缓存
  - 例如 Nginx 中默认的缓存关键字：proxy_cache_key $scheme$proxy_host$request_uri;
* 缓存中的响应允许当前请求的方法使用缓存
* 缓存中的响应 Vary 头部指定的头部必须与请求中的头部相匹配：
  - Vary = “*” / 1#field-name
    - Vary: *意味着一定匹配失败
* 当前请求以及缓存中的响应都不包含 no-cache 头部（Pragma: no-cache 或者 Cache-Control: no-cache）
* 缓存中的响应必须是以下三者之一：
  - 新鲜的（时间上未过期）
  - 缓存中的响应头部明确告知可以使用过期的响应（如 Cache-Control: max-stale=60）
  - 使用条件请求去服务器端验证请求是否过期，得到 304 响应

### Warning 头部：对响应码进行补充（缓存或包体转换）

* Warning = 1#warning-value
  - warning-value = warn-code SP warn-agent SP warn-text [ SP warn-date ]
    - warn-code = 3DIGIT
    - warn-agent = ( uri-host [ ":" port ] ) / pseudonym
    - warn-text = quoted-string
    - warn-date = DQUOTE HTTP-date DQUOTE
* 常见的 warn-code
  - Warning: 110 - "Response is Stale“
  - Warning: 111 - "Revalidation Failed“
  - Warning: 112 - "Disconnected Operation“
  - Warning: 113 - "Heuristic Expiration“
  - Warning: 199 - "Miscellaneous Warning“
  - Warning: 214 - "Transformation Applied“
  - Warning: 299 - "Miscellaneous Persistent Warning"

### 验证请求与响应
* 验证请求
  - 若缓存响应中含有 Last-Modified 头部
    - If-Unmodified-Since
    - If-Modified-Since
    - If-Range
  - 若缓存响应中含有 Etag 头部
    - If-None-Match
    - If-Match
    - If-Range

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
