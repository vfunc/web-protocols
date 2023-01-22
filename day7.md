# 《Web 协议详解与抓包实战》学习笔记 Day 7

## 问题：如何传递 IP 地址？

* TCP 连接四元组（src ip,src port, dst ip,dst port）
* HTTP 头部 X-Real-IP 用于传递用户 IP
* HTTP 头部 X-Forwarded-For 用于传递 IP
* 网络中存在许多反向代理

## 请求的上下文: User-Agent

指明客户端的类型信息，服务器可以据此对资源的表述做抉择

* User-Agent = product *( RWS ( product / comment ) )
  - product = token ["/" product-version]
  - RWS = 1*( SP / HTAB )

## 请求的上下文: Referer

浏览器对来自某一页面的请求自动添加的头部

* Referer = absolute-URI / partial-URI
* Referer 不会被添加的场景
  - 来源页面采用的协议为表示本地文件的 "file" 或者 "data" URI
  - 当前请求页面采用的是 http 协议，而来源页面采用的是 https 协议
* 服务器端常用于统计分析、缓存优化、防盗链等功能

## 请求的上下文: From

主要用于网络爬虫，告诉服务器如何通过邮件联系到爬虫的负责人
From = mailbox

## 响应的上下文：Server

指明服务器上所用软件的信息，用于帮助客户端定位问题或者统计数据

Server = product *( RWS ( product / comment ) )
- product = token ["/" product-version]

## 响应的上下文： Allow 与 Accept-Ranges

* Allow：告诉客户端，服务器上该 URI 对应的资源允许哪些方法的执行
- Allow = #method
  - 例如：Allow: GET, HEAD, PUT
* Accept-Ranges：告诉客户端服务器上该资源是否允许 range 请求
- Accept-Ranges = acceptable-ranges
  - 例如：Accept-Ranges: bytes
  - Accept-Ranges: none

## 内容协商

每个 URI 指向的资源可以是任何事物，可以有多种不同的表述，例如一份 文档可以有不同语言的翻译、不同的媒体格式、可以针对不同的浏览器提 供不同的压缩编码等。

## 内容协商的两种方式
* Proactive 主动式内容协商：
  - 指由客户端先在请求头部中提出需要的表述形式，而服务器根据这些请求 头部提供特定的 representation 表述
* Reactive 响应式内容协商：
  - 指服务器返回 300 Multiple Choices 或者 406 Not Acceptable，由客户端 选择一种表述 URI 使用
* 常见的协商要素
  - 质量因子 q：内容的质量、可接受类型的优先级
  - 媒体资源的 MIME 类型及质量因子
  - 字符编码：由于 UTF-8 格式广为使用，Accept-Charset 已被废弃
  - 内容编码：主要指压缩算法
  - 表述语言

## 国际化与本地化
* internationalization（i18n，i 和 n 间有 18 个字符）
  - 指设计软件时，在不同的国家、地区可以不做逻辑实现层面的修改便能够以不 同的语言显示
* localization（l10n，l 和 n 间有 10 个字符）
  - 指内容协商时，根据请求中的语言及区域信息，选择特定的语言作为资源表述

## 资源表述的元数据头部
* 媒体类型、编码
  - content-type: text/html; charset=utf-8
* 内容编码
  - content-encoding: gzip
* 语言
  - Content-Language: de-DE, en-CA

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
