# 《Web 协议详解与抓包实战》学习笔记 Day 5

## 请求行

* request-line = method SP request-target SP HTTP-version CRLF
  - method 方法：指明操作目的，动词
  - request-target = origin-form / absolute-form / authority-form / asterisk-form
* request-target = origin-form / absolute-form / authority-form / asterisk-form
  - origin-form = absolute-path [ "?" query ]
  - 向 origin server 发起的请求，path 为空时必须传递 /
  - absolute-form = absolute-URI
  - 仅用于向正向代理 proxy 发起请求时，详见正向代理与隧道
  - authority-form = authority
  - 仅用于 CONNECT 方法，例如 CONNECT www.example.com:80 HTTP/1.1
  - asterisk-form = "*“
  - 仅用于 OPTIONS 方法
* HTTP-version 版本号发展历史：https://www.w3.org/Protocols/History.html
  - HTTP/0.9：只支持 GET 方法，过时
  - HTTP/ 1.0：RFC1945，1996， 常见使用于代理服务器（例如 Nginx 默认配置）
  - HTTP/ 1.1：RFC2616，1999
  - HTTP/ 2.0：2015.5 正式发布
* 常见方法（RFC7231）
  - GET：主要的获取信息方法，大量的性能优化都针对该方法，幂等方法
  - HEAD：类似 GET 方法，但服务器不发送 BODY，用以获取 HEAD 元数据，幂等方法
  - POST：常用于提交 HTML FORM 表单、新增资源等
  - PUT：更新资源，带条件时是幂等方法
  - DELETE：删除资源，幂等方法
  - CONNECT：建立 tunnel 隧道
  - OPTIONS：显示服务器对访问资源支持的方法，幂等方法
  - TRACE：回显服务器收到的请求，用于定位问题。有安全风险
* 用于文档管理的 WEBDAV 方法(RFC2518)
  - PROPFIND：从 Web 资源中检索以 XML 格式存储的属性。它也被重载，以允许一个检索远程系统的集合结构（也叫目录层次结构）
  - PROPPATCH：在单个原子性动作中更改和删除资源的多个属性
  - MKCOL：创建集合或者目录
  - COPY：将资源从一个 URI 复制到另一个 URI
  - MOVE：将资源从一个 URI 移动到另一个 URI
  - LOCK：锁定一个资源。WebDAV 支持共享锁和互斥锁。
  - UNLOCK：解除资源的锁定

## HTTP 响应行

* status-line = HTTP-version SP status-code SP reason-phrase CRLF
  - status-code = 3DIGIT
  - reason-phrase = *( HTAB / SP / VCHAR / obs-text )

* 响应码分类：1xx
  - 响应码规范：RFC6585 （2012.4）、RFC7231（2014.6）
  - 1xx：请求已接收到，需要进一步处理才能完成，HTTP1.0 不支持
    - 100 Continue：上传大文件前使用，由客户端发起请求中携带 Expect: 100-continue 头部触发
    - 101 Switch Protocols：协议升级使用，由客户端发起请求中携带 Upgrade: 头部触发，如升级 websocket 或者 http/2.0
    - 102 Processing：WebDAV 请求可能包含许多涉及文件操作的子请求，需要很长时间才能完成请求。该代码表示服务器已经收到并正在处理请求，但无响应可用。这样可以防止客户端超时，并假设请求丢失

* 响应码分类：2xx
  - 2xx：成功处理请求
    - 200 OK: 成功返回响应
    - 201 Created: 有新资源在服务器端被成功创建
    - 202 Accepted: 服务器接收并开始处理请求，但请求未处理完成。这样一个模糊的概念是有意如此设计，可以覆盖更多的场景。例如异步、需要长时间处理 的任务
    - 203 Non-Authoritative Information：当代理服务器修改了 origin server 的 原始响应包体时（例如更换了HTML中的元素值），代理服务器可以通过修改 200为203的方式告知客户端这一事实，方便客户端为这一行为作出相应的处理。 203响应可以被缓存
    - 204 No Content：成功执行了请求且不携带响应包体，并暗示客户端无需 更新当前的页面视图
    - 205 Reset Content：成功执行了请求且不携带响应包体，同时指明客户端需要更新当前页面视图
    - 206 Partial Content：使用 range 协议时返回部分响应内容时的响应码
    - 207 Multi-Status：RFC4918 ，在 WEBDAV 协议中以 XML 返回多个资源 的状态
    - 208 Already Reported：RFC5842 ，为避免相同集合下资源在207响应码下重复上报，使用 208 可以使用父集合的响应码

* 响应码分类：3xx
  - 3xx：重定向使用 Location 指向的资源或者缓存中的资源。在 RFC2068 中规定客户端重定向次数不应超过 5 次，以防止死循环
    - 300 Multiple Choices：资源有多种表述，通过 300 返回给客户端后由其自行选择访问哪一种表述。由于缺乏明确的细节，300 很少使用
    - 301 Moved Permanently：资源永久性的重定向到另一个 URI 中
    - 302 Found：资源临时的重定向到另一个 URI 中
    - 303 See Other：重定向到其他资源，常用于 POST/PUT 等方法的响应中
    - 304 Not Modified：当客户端拥有可能过期的缓存时，会携带缓存的标识 etag、时间等信息询问服务器缓存是否仍可复用，而304是告诉客户端可以复用缓存
    - 307 Temporary Redirect：类似302，但明确重定向后请求方法必须与原请求方法相同，不得改变
    - 308 Permanent Redirect：类似301，但明确重定向后请求方法必须与原请求方法相同，不得改变

* 响应码分类：4xx
  - 4xx：客户端出现错误
    - 400 Bad Request：服务器认为客户端出现了错误，但不能明确判断为以下哪种错误时使用此错误码。例如HTTP请求格式错误
    - 401 Unauthorized：用户认证信息缺失或者不正确，导致服务器无法处理请求
    - 407 Proxy Authentication Required：对需要经由代理的请求，认证信息未通过代理服务器的验证
    - 403 Forbidden：服务器理解请求的含义，但没有权限执行此请求
    - 404 Not Found：服务器没有找到对应的资源
    - 410 Gone：服务器没有找到对应的资源，且明确的知道该位置永久性找不到该资源
    - 405 Method Not Allowed：服务器不支持请求行中的 method 方法
    - 406 Not Acceptable：对客户端指定的资源表述不存在（例如对语言或者编码有要求），服务器返回表述列表供客户端选择
    - 408 Request Timeout：服务器接收请求超时
    - 409 Conflict：资源冲突，例如上传文件时目标位置已经存在版本更新的资源
    - 411 Length Required：如果请求含有包体且未携带 Content-Length 头部，且不属于 chunk类请求时，返回 411
    - 412 Precondition Failed：复用缓存时传递的 If-Unmodified-Since 或 If- None-Match 头部不被满足
    - 413 Payload Too Large/Request Entity Too Large：请求的包体超出服务器能处理的最大长度
    - 414 URI Too Long：请求的 URI 超出服务器能接受的最大长度
    - 415 Unsupported Media Type：上传的文件类型不被服务器支持
    - 416 Range Not Satisfiable：无法提供 Range 请求中指定的那段包体
    - 417 Expectation Failed：对于 Expect 请求头部期待的情况无法满足时的响应码
    - 421 Misdirected Request：服务器认为这个请求不该发给它，因为它没有能力处理
    - 426 Upgrade Required：服务器拒绝基于当前 HTTP 协议提供服务，通过 Upgrade 头部告知客户端必须升级协议才能继续处理
    - 428 Precondition Required：用户请求中缺失了条件类头部，例如 If-Match
    - 429 Too Many Requests：客户端发送请求的速率过快
    - 431 Request Header Fields Too Large：请求的 HEADER 头部大小超过限制
    - 451 Unavailable For Legal Reasons：RFC7725 ，由于法律原因资源不可访问

* 响应码分类：5xx
  - 5xx：服务器端出现错误
    - 500 Internal Server Error：服务器内部错误，且不属于以下错误类型
    - 501 Not Implemented：服务器不支持实现请求所需要的功能
    - 502 Bad Gateway：代理服务器无法获取到合法响应
    - 503 Service Unavailable：服务器资源尚未准备好处理当前请求
    - 504 Gateway Timeout：代理服务器无法及时的从上游获得响应
    - 505 HTTP Version Not Supported：请求使用的 HTTP 协议版本不支持
    - 507 Insufficient Storage：服务器没有足够的空间处理请求
    - 508 Loop Detected：访问资源时检测到循环
    - 511 Network Authentication Required：代理服务器发现客户端需要进 行身份验证才能获得网络访问权限

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
