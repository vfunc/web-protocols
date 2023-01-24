# 《Web 协议详解与抓包实战》学习笔记 Day 9

## HTML FORM 表单

* HTML：HyperText Markup Language，结构化的标记语言（非编程语言）
  - 浏览器可以将 HTML 文件渲染为可视化网页
* FORM 表单：HTML 中的元素，提供了交互控制元件用来向服务器通过 HTTP 协议提交信 息，常见控件有：
  - Text Input Controls：文本输入控件
  - Checkboxes Controls：复选框控件
  - Radio Box Controls ：单选按钮控件
  - Select Box Controls：下拉列表控件
  - File Select boxes：选取文件控件
  - Clickable Buttons：可点击的按钮控件
  - Submit and Reset Button：提交或者重置按钮控件
* HTML FORM 表单提交请求时的关键属性
  - action：提交时发起 HTTP 请求的 URI
  - method：提交时发起 HTTP 请求的 http 方法
    - GET：通过 URI，将表单数据以 URI 参数的方式提交
    - POST：将表单数据放在请求包体中提交
  - enctype：在 POST 方法下，对表单内容在请求包体中的编码方式
    - application/x-www-form-urlencoded
      - 数据被编码成以 ‘&’ 分隔的键-值对, 同时以 ‘=’ 分隔键和值，字符以 URL 编码方式编码
    - multipart/form-data
      - boundary 分隔符
      - 每部分表述皆有HTTP头部描述子包体，例如 Content-Type
      - last boundary 结尾
* multipart(RFC1521)：一个包体中多个资源表述
  - Content-type 头部指明这是一个多表述包体
    - Content-type: multipart/form-data; boundary=----WebKitFormBoundaryRRJKeWfHPGrS4LKe
  - Boundary 分隔符的格式
    - boundary := 0*69<bchars> bcharsnospace
      - bchars := bcharsnospace / " "
      - bcharsnospace := DIGIT / ALPHA / "'" / "(" / ")" / "+" / "_" / "," / "-" / "." / "/" / ":" / "=" / "?"

## Multipart 包体格式(RFC822)

* multipart-body = preamble 1*encapsulation close-delimiter epilogue
  - preamble := discard-text
  - epilogue := discard-text
    - discard-text := *(*text CRLF)
  - 每部分包体格式：encapsulation = delimiter body-part CRLF
    - delimiter = "--" boundary CRLF
    - body-part = fields *( CRLF *text )
      - field = field-name ":" [ field-value ] CRLF
        - content-disposition: form-data; name="xxxxx"
        - content-type 头部指明该部分包体的类型
  - close-delimiter = "--" boundary "--" CRLF

## 多线程、断点续传、随机点播等场景的步骤

* 客户端明确任务：从哪开始下载
  - 本地是否已有部分文件
    - 文件已下载部分在服务器端发生改变？
  - 使用几个线程并发下载
* 下载文件的指定部分内容
* 下载完毕后拼装成统一的文件

## HTTP Range规范(RFC7233)

* 允许服务器基于客户端的请求只发送响应包体的一部分给到客户端，而客户端 自动将多个片断的包体组合成完整的体积更大的包体
  - 支持断点续传
  - 支持多线程下载
  - 支持视频播放器实时拖动
* 服务器通过 Accept-Range 头部表示是否支持 Range 请求
  - Accept-Ranges = acceptable-ranges
  - 例如：
    - Accept-Ranges: bytes：支持
    - Accept-Ranges: none：不支持
* Range 请求范围的单位 基于字节，设包体总长度为 10000
  - 第 1 个 500 字节：bytes=0-499
  - 第 2 个 500 字节
    - bytes=500-999
    - bytes=500-600,601-999
    - bytes=500-700,601-999
  - 最后 1 个 500 字节：
    - bytes=-500
    - bytes=9500-
    - 仅要第 1 个和最后 1 个字节：bytes=0-0,-1
  - 通过Range头部传递请求范围，如：Range: bytes=0-499

## Range 条件请求
  - 如果客户端已经得到了 Range 响应的一部分，并想在这部分响应未过期 的情况下，获取其他部分的响应
    - 常与 If-Unmodified-Since 或者 If-Match 头部共同使用
  - If-Range = entity-tag / HTTP-date
    - 可以使用 Etag 或者 Last-Modified

## 服务器响应
  - 206 Partial Content
    - Content-Range 头部：显示当前片断包体在完整包体中的位置
    - Content-Range = byte-content-range / other-content-range
      - byte-content-range = bytes-unit SP ( byte-range-resp / unsatisfied-range )
        - byte-range-resp = byte-range "/" ( complete-length / "*" )
          - complete-length = 1*DIGIT
            - 完整资源的大小，如果未知则用 * 号替代
          - byte-range = first-byte-pos "-" last-byte-pos
  - 416 Range Not Satisfiable
    - 请求范围不满足实际资源的大小，其中 Content-Range 中的 complete- length 显示完整响应的长度，例如：
      - Content-Range: bytes */1234
  - 200 OK
    - 服务器不支持 Range 请求时，则以 200 返回完整的响应包体

## 多重范围与 multipart
  - 请求：
    - Range: bytes=0-50, 100-150
  - 响应：
    - Content-Type：multipart/byteranges; boundary=…

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
