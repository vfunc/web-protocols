# 《Web 协议详解与抓包实战》学习笔记 Day 13

## HTTP 缓存

### 为当前请求复用前请求的响应

* 目标：减少时延；降低带宽消耗
* 可选而又必要

### 私有缓存与共享缓存

* 私有缓存：仅供一个用户使用的缓存，通常只存在于如浏览器这样的客户端上
* 共享缓存：可以供多个用户的缓存，存在于网络中负责转发消息的代理服务器（对热点资源常使用共享缓存，以减轻源服务器的压力，并提升网络效率）
  - Authentication 响应不可被代理服务器缓存
  - 正向代理
  - 反向代理

### Age 头部及 current_age 的计算

* Age 表示自源服务器发出响应（或者验证过期缓存），到使用缓存的响应发出 时经过的秒数
  - 对于代理服务器管理的共享缓存，客户端可以根据 Age 头部判断缓存时间
    - Age = delta-seconds
    - current_age
  - 计算：current_age = corrected_initial_age + resident_time;
    - resident_time = now - response_time(接收到响应的时间);
    - corrected_initial_age = max(apparent_age, corrected_age_value);
      - corrected_age_value = age_value + response_delay;
        - response_delay = response_time - request_time(发起请求的时间);
      - apparent_age = max(0, response_time - date_value);

### Cache-Control 头部

* Cache-Control = 1#cache-directive
  - cache-directive = token [ "=" ( token / quoted-string ) ]
    - delta-seconds = 1*DIGIT
      - RFC 规范中的要求是，至少能支持到 2147483648 (2^31)
* 请求中的头部：max-age、max-stale、min-fresh、no-cache、no- store、no-transform、only-if-cached
* 响应中的头部： max-age、s-maxage 、 must-revalidate 、proxy- revalidate 、no-cache、no-store、no-transform、public、private

### Cache-Control 头部在请求中的值

*  max-age：告诉服务器，客户端不会接受 Age 超出 max-age 秒的缓存
*  max-stale：告诉服务器，即使缓存不再新鲜，但陈旧秒数没有超出 max-stale 时，客户端仍 打算使用。若 max-stale 后没有值，则表示无论过期多久客户端都可使用
*  min-fresh：告诉服务器，Age 至少经过 min-fresh 秒后缓存才可使用
*  no-cache：告诉服务器，不能直接使用已有缓存作为响应返回，除非带着缓存条件到上游服 务端得到 304 验证返回码才可使用现有缓存
*  no-store：告诉各代理服务器不要对该请求的响应缓存（实际有不少不遵守该规定的代理服务 器）
*  no-transform：告诉代理服务器不要修改消息包体的内容
*  only-if-cached：告诉服务器仅能返回缓存的响应，否则若没有缓存则返回 504 错误码
*  must-revalidate：告诉客户端一旦缓存过期，必须向服务器验证后才可使用
*  proxy-revalidate：与 must-revalidate 类似，但它仅对代理服务器的共享缓存 有效
*  no-cache：告诉客户端不能直接使用缓存的响应，使用前必须在源服务器验证 得到 304 返回码。如果 no-cache 后指定头部，则若客户端的后续请求及响应 中不含有这些头则可直接使用缓存
*  max-age：告诉客户端缓存 Age 超出 max-age 秒后则缓存过期
*  s-maxage：与 max-age 相似，但仅针对共享缓存，且优先级高于 max-age 和 Expires
*  public：表示无论私有缓存或者共享缓存，皆可将该响应缓存
*  private：表示该响应不能被代理服务器作为共享缓存使用。若 private 后指定头 部，则在告诉代理服务器不能缓存指定的头部，但可缓存其他部分
*  no-store：告诉所有下游节点不能对响应进行缓存
*  no-transform：告诉代理服务器不能修改消息包体的内容

> [课程链接《Web 协议详解与抓包实战》极客时间](http://gk.link/a/11UWp)
