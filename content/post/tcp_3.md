---
title: "TCP/IP学习理解(三)"
date: 2018-07-16T16:13:50+08:00
categories: [TCP/IP]
gitment: true
TableOfContents: true
---

# 以太网帧格式

## 类型
* <b>Ethernet II:</b>以太网第二版或者称之为Ethernet II帧，DIX帧，是最常见的帧类型。并通常直接被IP协议使用。
* <b>IEEE 802.3:</b>Novell的非标准IEEE 802.3帧变种。
* <b>IEEE 802.2:</b>逻辑链路控制 (LLC) 帧
* <b>SNAP:</b>子网接入协议(SNAP)帧

### Ethernet II通常是用于IP协议,包括的字段

|字段|长度|备注|
|----|----|----|
|目的MAC地址|6个字节|<i>因为后来源IP 目的IP 源端口 目的端口信息中，源始终是放在前面的，而这里目的MAC放在了前面，原因在于现在转发形式为存储转发，而在这里还有一条转发方式叫做快速转发，即只查找目的MAC地址，即直接转发，所以将目的MAC放在前面查找的过程中速度会加快</i>|
|源MAC地址|6个字节|-|
|协议类型|2个字节|-|
<br />

|协议|标识|
|----|----|
|IPv4|0x0800|
|IPX|0x8137| 
|ARP|0x0806| 
|Pause Control|0x8808| 
|IPv6|0x86DD|

<hr />

# IP头部报文
<table style="zoom:0.8" > <thead> <tr> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> <th colspan="1"> 1 </th> </tr> </thead> <tbody> <tr> <td colspan="4"> <b>版本</b> <i>4位</i> </td> <td colspan="4"> <b>包头长度</b> <i>4位</i> </td> <td colspan="8"> <b>服务类型</b> <i>8位</i> </td> <td colspan="16"> <b>长度</b> <i>16位</i> </td> </tr> <tr> <td colspan="16"> <b>标识</b> <i>16位</i> </td> <td colspan="3"> <b>标志</b> <i>3位</i> </td> <td colspan="13"> <b>段偏移量</b> <i>13位</i> </td> </tr> <tr> <td colspan="8"> <b>TTL</b> <i>4位</i> </td> <td colspan="8"> <b>协议</b> <i>4位</i> </td> <td colspan="16"> <b>校验和</b> <i>16位</i> </td> </tr> </tr> <tr> <td colspan="32"> <b>源IP地址</b> <i>32位</i> </td> </tr> <tr> <td colspan="32"> <b>目的IP地址</b> <i>32位</i> </td> </tr> </tbody> </table>

## IP包头字段说明

### 版本：4位，指定IP协议的版本号。

### 包头长度(IHL)：
> 4位，IP协议包头的长度，指明IPv4协议包头长度的字节数包含多少个32位。

> 由于IPv4的包头可能包含可变数量的可选项，所以这 个字段可以用来确定IPv4数据报中数据部分的偏移位置。

> IPv4包头的最小长度是20个字节，因此IHL这个字段的最小值用十进制表示就是5 (5x4 = 20字节)。就是说，它表示的是包头的总字节数是4字节的倍数。

> 可以表示的范围是0到65535之间。这里的总长度字段不包括帧头和帧尾，只是IP报文的总长度（包括IP头部）。 

### 服务类型：
> 定义IP协议包的处理方法，它包含如下子字段

> 过程字段：3位，设置了数据包的重要性，取值越大数据越重要，取值范围为：0（正常）~ 7（网络控制）

> 延迟字段：1位，取值：0（正常）、1（期特低的延迟）

> 流量字段：1位，取值：0（正常）、1（期特高的流量）

> 可靠性字段：1位，取值：0（正常）、1（期特高的可靠性）

> 成本字段：1位，取值：0（正常）、1（期特最小成本）

> 未使用：1位

### 长度：
> IP包的总长

### 标识：
> 该字段的表示范围是0到65535之间，标识在长报文分片后，该报文位于分片中的第几片信息
### 标志：
> 是一个3位的控制字段，包含：

> 保留位：1位

> 不分段位：1位，取值：0（允许数据报分段）、1（数据报不能分段）

> 更多段位：1位，取值：0（数据包后面没有包，该包为最后的包）、1（数据包后面有更多的包）


### 段偏移量：
> 当数据分组时，它和更多段位（MF, More fragments）进行连接，帮助目的主机将分段的包组合。

### TTL：
> 表示数据包在网络上生存多久，每通过一个路由器该值减一，为0时将被路由器丢弃。
>同时向源地址发送错误报告，促使重新发送。默认值为64，在某些特殊的报文中，如组播报文，该位会被置1。

### 协议：
> 8位，这个字段定义了IP数据报的数据部分使用的协议类型。常用的协议及其十进制数值包括ICMP(1)、TCP(6)、UDP(17)。

### 校验和：
> 16位，是IPv4数据报包头的校验和。

### 源地址（Source Address，SA）

> 发送IP数据包的IP地址，占32位。

### 目的地址（Destination Address）

> 接收IP数据包的IP地址，也占32位。