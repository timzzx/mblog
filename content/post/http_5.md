---
title: "HTTP学习理解(五)"
date: 2018-07-31T14:08:30+08:00
categories: [HTTP]
tags: [2018-07]
TableOfContents: true
---
# HTTP首部

## HTTP报文首部

### HTTP请求报文：

在请求中， HTTP报文由方法， URL， HTTP版本和HTTP首部字段等构成；

### HTTP响应报文：

在响应中， HTTP报文由HTTP版本， 状态码， HTTP首部三个部分组成。

## HTTP首部字段

在客户端和服务器之间以HTTP协议进行通信的过程中， 无论是请求还是响应都会使用到首部
字段， 它能起到传递额外重要信息的作用。

### 四种HTTP首部字段类型：

<i class="fa fa-angle-double-right" ></i>&nbsp;<b>通用首部字段（General Header Fields）:</b>请求报文和响应报文两方都会使用的首部；<br /><br />
<i class="fa fa-angle-double-right" ></i>&nbsp;<b>请求首部字段（Request Header Fields）:</b>从客户端向服务器发送请求报文时使用的首部。补充了请求的附加内容，客户端的信息，响应内容相关的优先级等信息。<br /><br />
<i class="fa fa-angle-double-right" ></i>&nbsp;<b>响应首部字段（Response Header Fields）:</b>从服务器向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。<br /><br />
<i class="fa fa-angle-double-right" ></i>&nbsp;<b>实体首部字段（Entity Header Fields）:</b>针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的信息。<br />
<hr />

# HTTP首部字段一览表

## 通用首部字段
|首部字段名| 说明|
|---|---|
|Cache-Control| 控制缓存的行为|
|Connection| 逐跳首部， 连接的管理|
|Date |创建报文的日期时间|
|Pragna| 报文指令|
|Trailer| 报文末端的首部一览|
|Transfer-Encoding |指定报文主体的传输编码方式|
|Upgrade |升级为其他协议|
|Via| 代理服务器的相关信息|
|Warning |错误通知|

## 请求首部字段

|首部字段名 |说明|
|---|---|
|Accept |用户代理可处理的媒体类型|
|Accept—Charset| 优先的字符集|
|Accept-Encoding| 优先的内容编码|
|Accept-Language |优先的语言（自然语言）|
|Authorization |Web认证信息|
|Expect| 期待服务器的指定行为|
|From |用户的电子邮箱地址|
|Host| 请求资源所在服务器|
|if-Match |比较实体标记（ETag）|
|if-Modified-Since| 比较资源的更新时间|
|if-None-Match| 比较实体标记（与if-Match相反）|
|if-Range |资源为更新时发送实体Byte的范围请求|
|if-Unmodified-Since |比较资源的更新时间（与if-Modified-Since相反）|
|Max-Forwards |最大传输逐跳数|
|Proxy-Authorization |代理服务器要求客户端的认证信息|
|Range |实体字节范围请求|
|Referer |对请求中的URL的原始获取方法|
|TE |传输编码的优先级|
|User-Agent| HTTP客户端程序的信息|

## 响应首部字段

|首部字段名| 说明|
|---|---|
|Accept-Ranges| 是否接受字节范围请求|
|Age |推算资源创建经过时间|
|ETag |资源的匹配信息|
|Location| 令客户端重定向至指定的URL|
|Proxy-Authenticate| 代理服务器对客户端的认证信息|
|Rety-After| 对再次发起请求的时机要求|
|Server| HTTP服务器的安装信息|
|Vary| 代理服务器缓存的管理信息|
|WWW-Authenticate| 服务器对客户端的认证信息|

## 实体首部字段

|首部字段名 |说明|
|---|---|
|Allow |资源科支持的HTTP方法|
|Content-Encoding| 实体主体适用的编码方式|
|Content-Language| 实体主体的自然语言|
|Content-Length| 实体主体的大小（单位：字节）|
|Content-Location| 替代对资源的URL|
|Content-MD5| 实体主体的报文摘要|
|Content-Range| 实体主体的位置范围|
|Content-Type| 实体主体的媒体类型|
|Expires| 实体主体过期的日期时间|
|Last-Modified| 资源的最后修改日期时间|

## 为Cookie服务的首部字段

|首部字段名| 说明 |首部类型|
|---|---|---|
|Set-Cookie |开始状态管理所有的Cookie信息| 响应首部字段|
|Cookie |服务器接收到的Cookie信息| 请求首部字段|

## Set—Cookie字段的属性

|属性 |说明|
|---|---|
|NAME=VALUE| 赋予Cookie的名称和其值|
|expires=DATE| Cookie的有效期（若不mingque指定则默认为浏览器关闭前为止）|
|path=PATH |将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的目录）|
|domain=域名| 作为Cookie适用对象的域名（若不指定则默认为创建Cookie的服务器的域名）|
|Scure |仅在HTTPS安全通信时才会发送Cookie|
|HttpOnly |加以限制， 使Cookie不能被JavaScript脚本访问|