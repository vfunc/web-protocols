# 《Web 协议详解与抓包实战》学习笔记 Day 4

## Chrome 抓包，快速定位 HTTP 协议问题

* <https://developer.chrome.com/docs/devtools/#network>
* 打开 Network 面板的快捷键：command + option + I (macOS) or Ctrl + Shift + I (Windows)

## URI 的基本格式以及与 URL 的区别

* `URL`：RFC1738（1994.12），Uniform Resource Locator，表示资源的位置，期望提供查找资源的方法
* `URN`：RFC2141（1997.5），Uniform Resource Name，期望为资源提供持久的、位置无关的标识方式，并允许简单地将多个命名空间映射到单个 URN 命名空间
* `URI`：RFC1630（1994.6）、RFC3986（2005.1，取代 RFC2396 和 RFC2732 ），Uniform Resource Identifier，用以区分资源，是 URL 和 URN 的超集，用以取代 URL 和 URN 概念

## Uniform Resource Identifier 统一资源标识符

### Uniform 统一

* 允许不同种类的资源在同一上下文中出现
* 对不同种类的资源标识符可以使用同一种语义进行解读
* 引入新标识符时，不会对已有标识符产生影响
* 允许同一资源标识符在不同的、internet 规模下的上下文中出现

### Resource 资源

* 可以是图片、文档、今天杭州的温度等，也可以是不能通过互联网访问的实体，例如人、 公司、实体书，也可以是抽象的概念，例如亲属关系或者数字符号
* 一个资源可以有多个 URI

### Identifier 标识符

* 将当前资源与其他资源区分开的名称

## URI 的组成

* 组成：schema, user information, host, port, path, query, fragment
* <https://www.rfc-editor.org/rfc/rfc7231#page-7>
* <https://www.rfc-editor.org/rfc/rfc7230#section-2.7>

## 为什么要进行 URI 编码

* 传递数据中，如果存在用作分隔符的保留字符怎么办？
* 对可能产生歧义性的数据编码
  - 不在 ASCII 码范围内的字符
  - ASCII 码中不可显示的字符
  - URI 中规定的保留字符
  - 不安全字符（传输环节中可能会被不正确处理），如空格、引号、尖括号等
* 保留字符
  - reserved = gen-delims / sub-delims
  - gen-delims = `:`, `/`, `?`, `#`, `[`, `]`, `@`
  - sub-delims = `!`, `$`, `&`, `'`, `(`, `)`, `*`, `+`, `,`, `;`, `=`
* 非保留字符
  - unreserved = `ALPHA` / `DIGIT` / `-`, `.`, `_`, `~`
  - ALPHA: %41-%5A and %61-%7A
  - DIGIT: %30-%39
  - -: %2D .: %2E _: %5F
  - ~: %7E，某些实现将其认为保留字符

* URI 百分号编码
  - 百分号编码的方式
    - pct-encoded = "%" HEXDIG HEXDIG
    - US-ASCII：128 个字符（95 个可显示字符，33 个不可显示字符）
    - 参见：https://zh.wikipedia.org/wiki/ASCII
  - 对于 HEXDIG 十六进制中的字母，大小写等价
  - 非 ASCII 码字符（例如中文）：建议先 UTF8 编码，再 US-ASCII 编码
  - 对 URI 合法字符，编码与不编码是等价的
