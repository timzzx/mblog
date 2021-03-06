---
title: "TCP/IP学习理解(六)"
date: 2018-07-18T11:14:14+08:00
categories: [TCP/IP]
tags: [2018-07]
TableOfContents: true
---

>RFC791中文-IP协议RFC 791 - 互联网协议<br />
>RFC:791<br />
>互联网协议<br />
>DARPA互联网程序<br />
>协议规范<br />
>1981.9<br />
>防御高级研究计划结构<br />
>信息处理技术办公室<br />
>1400 Wilson Boulevard<br />
>Arlington, Virginia  22209<br />
>南吉利福尼亚大学信息科研所<br />

# 前言

>本文档详细说明叻国防部(DoD)的标准互联网协议.文档基于APRA互联网协议的前六个早期版本.很多人对规范的概念以及正文作出过贡献.这个版本对寻址(addressing),错误处理(error handling ),选项编码(option codes),以及互联网的安全(security)、优先(predence),划分(compartments)、处理限制的特性作出了修订。<br />
>Jon Postel<br />
>Editor<br />
>September 1981<br />

## 1.介绍

### 1.1 出发点(Motivation)

Internet协议是为了在包交换(packet-switched)计算机通讯网络的互联系统中使用而设计的。我们称这样的系统为“catener”【1】。Internet协议提供从发起者(source)到目的地(destination)的块数据(数据报)的传输,发起者和目的地都是由固定长度的地址标识的主机(host)。如果需要, Internet协议也提供长数据报的分片(fragmentation)和重组(reassembly),以通过“小包”(small packet)网络传输大数据报。

### 1.2 范围(Scope)

internet协议仅限于提供在互联网络系统上从发起者到目的地投递一个internet数据报所需的功能。不提供增强端到端(end-to-end)的可靠性,流程控制(flow control),排序(sequencing),和其它主机到主机协议上常有的服务。internet协议可以支持网络提供不同类型和不同质量的服务(The internet protocol can capitalize
on the services of its supporting networks to provide various types  and qualities of service.)。

### 1.3 接口(Interfaces)

在internet环境中,本协议被视为主机到主机(host-to-host)的协议。本协议调用本地网络协议来携带internet数据报到下一个网关或者目的主机。
比如,TCP模块调用internet模块携带一个TCP分片(该分片包含了TCP头部和用户数据)作为一个internet数据报的数据部分。TCP模块会在internet头部中提供地址(addresses)和其他参数作为调用Internet模块的参数。然后internet模块会创建一个internet数据报并且调用本地网络接口来传输internet数据报。
比如,对于ARPANET,internet模块会调用一个本地网络模块(local net module),该模块会给internet数据报添加一个1822个字节的前导字符,创建一条传输给IMP的ARPANET消息。ARPANET 地址将通过本地网络接口从internet地址中获取,该地址是ARPANET中的某些主机的地址,该主机可能是个到其他网络的网关。

### 1.4 操作(operation)

internet协议执行两个基本功能:寻址(addressing)和分片(fragmentation).<br />
internet模块使用在internet头部中携带的地址来给目的地址传送internet数据报.传输路径的选择被称作选路(routing).<br />
internet模块使用internet头部中的域来分片和重组internet数据报,这在通过"小包"网络传输的情况下是必要的.<br />
操作模型是位于每个主机上的internet模块负责internet通信,位于每个网关上的internet模块负责网络互联.这些模块都具有解析地址,分片和重组数据报等相同的功能.另外,这些模块(特别是网关上的internet模块)具有选路和其他功能对应的程序.<br />
internet协议将每个internet数据报视为同任何其他internet数据报无关的独立实体.这里不存在连接或者逻辑回路(虚的或其他).<br />
internet协议使用4个主要的机制提供服务:服务类型(Type of Service),生存时间(Time to Live),操作(Operation)和校验和(Header  Checksum).<br />
服务类型用来指示要求的服务质量.服务类型是一个抽象的整套的参数,这些参数指定了组成internet的网络中提供的服务选择.这个服务指示类型在选路的时候被网关用来为某一个特定的网络,下一个网络或者下一个网关选择真实的传输参数.<br />
生存时间数据报生存时间的上限.它由数据报的发送者设定,在网络上每个点,当数据报被处理的时候,逐渐递减.如果生存时间在internet数据报到达目的地址前达到0值,internet数据报就被销毁.生存时间可以看作一个自我销毁时间限制.<br />
选项(options)提供了在某些情况下需要或有用的控制功能,但是大多数情况下是不必要的.选项包括时间戳(timestamp),安全(security)和特殊选路(special routing)
校验和提供了处理internet数据报使用到的信息被正确传输的确认.数据可能包含错误.如果校验和失败了,internet数据报就被检测到错误的实体立即丢弃.<br />
Internet协议并没有提供可靠传输机制.没有端对端或者逐跳(hop-by-hop)的确认机制.没有数据的错误控制,只有一个头部校验和.没有重传.没有流控.<br />
检测到的错误可以通过Internet 控制消息协议(ICMP)来报告,该协议在internet协议模块中实现.<br />

## 2.总览(OVERVIEW)

### 2.1 同其他协议的关系(Relation to Other Protocols)
下图展示了internet协议在协议层次中的地位:<br />
```go
+------+ +-----+ +-----+     +-----+  
|Telnet| | FTP | | TFTP| ... | ... |  
+------+ +-----+ +-----+     +-----+  
    |       |         |           |     
    +-----+     +-----+     +-----+  
    | TCP |     | UDP | ... | ... |  
    +-----+     +-----+     +-----+  
        |           |           |     
    +--------------------------+----+
    |    Internet Protocol & ICMP   |
    +--------------------------+----+
                    |                 
    +---------------------------+  
    |   Local Network Protocol  |  
    +---------------------------+  
```
<center>Protocol Relationships</center><br />
<center>Figure 1.</center><br />

Internet协议接口往上是高级别的主机到主机的协议,向下则是局域网络协议(local network protocol).在这里,局域网络("local network")可以是在一栋大楼里面的一个小网络,也可以是像ARPANET这样的大网络.

### 2.2 操作模型(Model of Operation)

从一个应用程序到其他应用程序的传输数据报操作模型可以通过如下例子展示:<br />
假设传输包括一个中间网关.<br />
发送程序准备好数据后,调用局域网络模块来发送那个数据(该数据作为一个数据报),在调用接口的时候传递目的地地址和其他参数.<br />
Internet模块组装数据报头部,并且给他附着一个数据.Internet模块为这个internet地址确定一个局域网络地址.在这里,它就是一个网关的地址.它发送这个数据报和局域网络地址给局域网络接口.<br />
局域网络接口创建一个局域网络头部,并为它附着一个数据报,然后通过局域网络传输.<br />
到达网关主机的数据报被封装(wrapped)在局域网络头部.局域网络接口去掉(strip off)头部,然后将数据报传送给internet模块.internet模块从internet地址确定数据报需要被发送到第二个网络的其他主机上.internet模块确定目的主机的局域网地址.调用局域网接口来发送数据报.<br />
这个局域网地址创建一个局域网头部并和数据报组装在一起,然后将组装结果发送给目的主机.<br />
在这个目的主机上,局域网接口去掉数据报的局域网头部,然后交给internet模块.<br />
internet模块确定数据报所要交付的应用程序.然后将数据作为一个系统调用的响应传递给应用程序,调用时传递了源地址和其他参数.<br />
```go
Application                                           Application
Program                                                   Program
\                                                   /
Internet Module      Internet Module      Internet Module
\                 /       \                /
LNI-1          LNI-1      LNI-2         LNI-2
\           /             \          /
Local Network 1           Local Network 2
```
<center>Transmission Path<br />
Figure 2<br /></center>

### 2.3.  功能描述(Function Description)

Internet协议的功能和目的是通过一个互联的网络传输数据报.这是通过从一个internet模块到另外一个internet模块传递数据报直到目的地址来实现.Internet模块位于主机上或者internet系统上的网关.数据报通过基于一个internet地址的解析从一个internet模块选路到另一个internet模块. 因此,internet协议的一个重要功能就是internet寻址.<br />
在从一个internet模块到另外一个internet模块的信息寻址中,数据报可能需要在一个最大包大小小于数据报大小的网络上传输.为了客服这个困难,在internet协议中提供了分片机制(fragmentation mechanism).<br />
寻址(Addressing)<br />
名字(names),地址(addresses)和路由(routes)是有区别的.名字指示了我们所要寻找的.地址告诉我们它在哪里.路由告诉我们如何到达哪里.internet协议主要处理地址.从域名到地址的映射是高级别的协议(如主机到主机或者应用程序)的任务.Internet模块将Internet地址映射为局域网络地址.将局域网络地址映射到路由是底层程序(如局域网络或者网关)的任务.<br />
地址固定为4段8位的地址(32位).以网络号码开始,接下来是本地地址(称为"其他"部分).有3种形式或者类型的internet地址:A类地址,第一段的最高位为0,其他7位为网络地址.剩下的24位位本地地址.B类地址,前两段为网络地址,其中前两个字节位10,其他14个字节为网络地址,后两段为本地地址.C类地址的前三段为网络地址,最后一段为本地地址.在前三段中,前三位为110,其他21位为网络地址.<br />
在将internet地址转化位局域网络地址的时候必须注意:单个物理主机必须能够被视为几个不同的主机,通过使用不同了internet地址。有些主机还可能有多个物理接口(multi-homing).<br />
也就是说,对一个主机来说,必须可以有多个网络的物理接口,每个接口可以有多个逻辑internet地址.<br />
地址映射的例子可以参考"Address Mappings"[5]<br />
分片(Fragmentation)<br />
当在一个允许大包大小的internet数据报的局域网络上产生,且必须在包大小为较小的局域网络上传输的时候,分片是必须的.<br />
一个数据报可以标为"不可分片".任何internet数据报如果这样标记,则在任何情况下,不能被分片.如果标记为"不可分片"的数据报除非分片不能到达目的地,则将其丢弃.<br />
在对internet协议模块不可视的局域网络上分片,传输和重组称为intranet分片,可以被使用.<br />
internet分片和重组程序需要能够将一个数据报分割成任意数量的块,这些块可以在之后重组.分片接收者使用identification头部来确保不同的分片不被混在一起.分片偏移(fragment offset)头部告诉接收者分片在原始数据报中的位置.分片偏移和分片长度(frament length)确定了这个分片所覆盖的原始数据报的块.more-fragments标志(通过重置)指示了最后一个分片.这些头部提供了足够的信息来重组数据报.<br />
identification头部被用来区分一个数据报的不同分片.internet数据报的生成(originating)协议模块设定identification头部为一个值,该值必须是对源地址和目的地址对和协议范围内在数据包的生存期间是唯一的(译者注:该值对同一source,destination,protocol在生存期间是唯一的).对于一个完整的数据报数据包的生成协议模块(originating protocol module)设定more-fragments标志为0,同时设定分片偏移为0.
为了对一个长internet数据报进行分片,internet协议模块(如网关),创建两个新的internet数据报,将internet头部内容从长数据报拷贝到两个新internet头部.长数据报的数据在8 octet的整数倍边界被分割成两部分(第二部分不必是8 octets的整数倍,但第一部分必须是).将第一部分8 octet块的数目为NFB(Number of Fragment Blocks:分片块个数).第一部分数据被放在第一个新的internet数据报,总长度头部设置成第一个数据报的长度.more-fragments标志设成1.数据的第二部分放在第二个新internet 数据报里面.总长度头部设置成第二个数据报的长度.more-fragments标志设置成同长数据报一样的值.第二个新internet数据报的片偏移头部值设置成长数据报的片偏移头部值加上NFB的和.这一过程可以通用化为n次分割,前面描述的是二分.<br />
要重组internet数据报的分片,internet协议模块(比如目的主机)将identification,目的地地址,源地址和协议四个头部值一样的internet数据报组合在一起.这一结合是通过将每个分片的数据块放在分片的
intenet头部中的片偏移标识的位置来实现的.第一个分片的分片偏移值为0,最后一个分片的more-fragments标志为0.<br />
### 2.4 网关(Gateways)

网关实现了internet协议,可以在网络间前推数据报.网关也实现了网关到网关的协议(GateWay to Gateway Protocol:GGP协议),用于协调选路和其他internet控制信息.
在网关中,高层协议需要被实现,且GGP功能被加到IP模块.<br />
```go
+-------------------------------+
| Internet Protocol & ICMP & GGP|
+-------------------------------+
        |                 |
+---------------+   +---------------+
|   Local Net   |   |   Local Net   |
+---------------+   +---------------+
```

<center>Gateway Protocols<br />
Figure 3.</center>

## 3.规范(SPECIFICATION)

### 3.1 Internet头部格式(Internet Header Format)

internet头部内容如下:<br />

```go
 0                   1                   2                   3   
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |Type of Service|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragment Offset    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |         Header Checksum       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source Address                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination Address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```
<center>Example Internet Datagram Header<br />
Figure 4.</center>
注:每个间隔代表一位.<br />
版本(version):4位<br />
版本头部指示了internet头部的格式.本文当描述的是版本4.<br />
Internet头部长度(IHL):  4 位<br />
internet头部长度是用32位表示的internet头部的长度,它指向数据的开始处.备注:一个正确头部的最小头部长度值是5.(译者注:5表示头部长度值为5*4=20 0ctets)<br />
服务类型(Type of  Service):8位<br />
服务类型提供了所要求的服务质量的抽象参数的指示.这些参数被用于在一个特定的网络上传输数据报时指导真实服务参数的选择.有些网络会提供服务优先级参数(service Precedence),高优先级的交通流比其他交通流重要一些(通常通过在高负载的时候仅接受高于某一特定优先级的交通流来实现).主要的选择是3方面的权衡:低延迟(low-delay),高可靠性(high-reliability),和高吞吐(high-throughput)..<br />
Bits 0-2:  Precedence(优先级).<br />
Bit    3:  0 = Normal Delay(正常延迟),      1 = Low Delay(低延迟).<br />
Bits   4:  0 = Normal Throughput(正常吞吐), 1 = High Throughput(高吞吐).<br />
Bits   5:  0 = Normal Relibility(正常可靠性), 1 = High Relibility(高可靠性).<br />
Bit  6-7:  Reserved for Future Use(保留).<br />

```go
    0     1     2     3     4     5     6     7
+-----+-----+-----+-----+-----+-----+-----+-----+
|                 |     |     |     |     |     |
|   PRECEDENCE    |  D  |  T  |  R  |  0  |  0  |
|                 |     |     |     |     |     |
+-----+-----+-----+-----+-----+-----+-----+-----+
Precedence(优先级)
111 - Network Control(网络控制)
110 - Internetwork Control(互联网络控制)
101 - CRITIC/ECP(至关重要)
100 - Flash Override(Flash覆盖)
011 - Flash
010 - Immediate(立即)
001 - Priority(主要)
000 - Routine(常规)
```
<br />
延迟(Delay),吞吐量(Troughout)和Reliability(可靠性)的指示可能会增加服务的代价.在很多网络中,某一个参数带来的高性能很可能带来其他参数的低性能.只有在一些非正常的情况下,这三个指示中两个必须设定.<br />
服务类型被用于在通过internet系统传输的时候指定数据报的处理.在Service Mappings[8]中给出了internet服务类型到真正的服务类型的映射的例子,如AUTODIN II,ARPANET,SATNET和PRNET.<br />
网络内控制优先权的指派仅在一个网络内使用.该指派的使用和控制是胜任于网络内的.网络间控制优先级的指派仅用于网关控制创作者.如果这些优先权指派的真实使用是同某一个特定网络相关的,它就负责那些优先级指派的权限控制和使用.<br />
总长度(Total Length):16位<br />
总长度是数据报的总长度,以字节(octets)为单位计量,包含internet头部和数据.这个头部允许一个数据报的长度最长到65535个字节.如此长的数据报对大部分主机和网络来说是不现实的. 所有主机必须能够接收长达576个字节的数据报(不管他们是以整个数据报到达还是以分片到达).推荐主机在确认目的地址能够接收大数据报的情况下才发送大于576字节的数据报.<br />
选择576这个数字是为了允许传送一个合理大小的数据块,除了要求的头部信息之外.比如,这个大小允许512个字节的数据块加上64个字节的头部填充于一个数据报.最大的internet头部是60个字节,通常internet头部是20个字节,允许高层协议流出一个头部富余量.<br />
标识(Identification):16位<br />
该标识由发送者设定值,有助于重组数据报的分片.<br />
标记(Flags):  3 位<br />
不同的控制标记:<br />
Bit 0: 保留,必须位0<br />
Bit 1: (DF) 0 = 可以分片(May Fragment),  1 =不可分片( Don't Fragment).<br />
Bit 2: (MF) 0 = 最后一个分片(Last Fragment), 1 = 还有分片(More Fragments).<br />

```go
  0   1   2
+---+---+---+
|   | D | M |
| 0 | F | F |
+---+---+---+
```

分片偏移(Fragment Offset):  13位<br />
该头部指示了这个分片在所属数据报中的位置.分片偏移以8字节(64位)作为计量单位.第一个分片的偏移为0.<br />
生存时间(Time to Live):8位该头部指示了数据报允许在internet系统中生存的最大时间.如果该头部的值为0,数据报必须被销毁.该头部在internet头部处理的时候被改变,该时间以秒单位度量,但由于处理数据报的每个模块必须减少TTL至少1秒,即便该模块处理数据报的时间少于1秒,TTL必须被视为数据报可以存在的时间的上限.这样做的目的是丢弃无法投递的数据报,限制数据报的生存时间协议(Protocol):8位<br />
该头部指示了在internet数据报的数据部分中使用的下一个层次的协议.不同协议的值在”Assigned Numbers”[9]中指定.<br />
头部校验和(Header Checksum):16位<br />
校验和仅用于头部.当某些头部(比如生存时间)改变的时候,检验和在每个internet头部被处理的时候被重新计算和确认.<br />
校验和的算法如下:<br />
检验和头部是头部中所有16位字的和,为了计算校验和,校验和头部的值是0.这是计算校验和和实验性证据的简单方法,这指示了它是一个充分的,但它是暂时的,可能被一个CRC程序替代,取决于未来的的实验.<br />
源地址(Source Address):32位,参见3.2<br />
目的地址(Destination Address):32位,参见3.2<br />
选项(Options):可变<br />
选项可以出现也可以不出现在数据报中.所有的IP模块(主机和网关)必须实现这个东东.可选的是在任一个特殊数据报中它们的传输,而不是它们的实现.<br />
在某些环境下,安全选项(security option)可能在所有的数据报中需要.<br />
Options头部在长度上是可变的.可以有0到多个的选项.option的格式有两种情况:<br />
情况1:一个单独的8位字节的选项类型(option-type)<br />
情况2:一个选项类型(option-type)八位字节,一个选项长度(option-length)八位字节和其他真实的选项数据八位字节.<br />
选项长度八位字节计入了选项类型八位字节.选项长度八位字节以及选项数据八位字节.<br />
选项类型八位字节有3部分:<br />
1 bit: 拷贝标志(copied flag)<br />
2 bits:选项类别(option class)<br />
5 bits:选项数目(option number)<br />
拷贝标志位指明该选项是否被拷贝到所有分片中.<br />
0 = 未被拷贝<br />
1 = 拷贝<br />
选项类别是:<br />
0 = 控制(control)<br />
1 = 保留<br />
2 = 调试和测量(debugging and measurement)<br />
3 = 保留<br />
定义了如下internet选项:<br />
```go
CLASS NUMBER LENGTH DESCRIPTION
----- ------ ------ -----------
  0     0      -    选项列表的结束.该选项仅占用1个八位字节,没有长度字节
  0     1      -    没有操作. 该选项仅占用1个八位字节,没有长度字节
  0     2     11    安全(Security). 用于携带安全(security),分隔(Compartmentation),用户组(User Group,TCC),和同DOD要求兼容的处理限制码。
  0     3     var.  宽松的源站(Loose Source Routing)用来基于源(source)提供的信息为internet数据报选路
  0     9     var.  严格源选路(Strict Source Routing)用来基于源(source)提供的信息为internet数据报选路
  0     7     var.  记录路径(Record Route).用来trace一个internet数据报经过的路径
  0     8      4    流ID(Stream ID).用来携带流标识
  2     4     var.  Interne时间戳
```
<center>特殊选项定义(Specific Option Definitions)<br /></center>

选项列表的结束<br />
```go
+--------+
|00000000|
+--------+
Type=0
```

该选项指示了选项列表的结束。这有可能同根据internet头部长度的internet头部的结束不一致。这用于所有选项的结束,而不是每一个选项的结束,并且仅在选项的结束同internet头部结束不一致的情况下需要使用。<br />
可以在分片或者其它原因时被拷贝,引入,或者删除<br />
无操作(No Operation)<br />
```go
 +--------+        
 |00000001|        
 +--------+          
Type=1
```         
这个选项可以在选项间使用,比如,可用以在32位边界对齐后续选项的开始部分。<br />
可以在分片或者其它原因时被拷贝,引入或者删除安全(Security)该选项为主机提供了发送安全(security),分隔(Compartmentation),处理限制,和TCC(关闭的用户组)参数。该选项的格式如下:<br />

```go
+ -------- + -------- + --- // --- + --- // --- + --- / / --- + --- // --- + 
| 10000010 | 00001011 | SSS SSS | CCC CCC | HHH HHH | TCC | 
+ -------- + -------- + --- // --- + --- // --- + --- // --- + --- / / --- + 
Type = 130 Length = 11        
```

安全(S 域): 16 位指定了安全的16个级别(其中的八个留作将来用) 
```go
00000000 00000000  - 未分类
11110001 00110101  - 机密
01111000 10011010  -  EFTO 
10111100 01001101  -  MMMM 
01011110 00100110  -  PROG 
10101111 00010011  - 受限
11010111 10001000  - 机密
01101011 11000101  - 最高机密
00110101 11100010  - （保留以备将来使用）
10011010 11110001  - （保留供将来使用） ）
01001101 01111000  - （保留供将来使用）
00100100 10111101  - （保留供将来使用）
00010011 01011110  - （保留供将来使用）
10001001 10101111  - （保留将来使用）
11000100 11010110  - （保留供将来使用）
11100010 01101011  - （保留将来使用）
       
```

分隔Compartments (C 域): 16 位当传输信息不被分隔的时候,使用全0值。其它值可以通过Defense Intelligence Agency 获取。<br />
处理限制Handling Restrictions (H 域): 16 位控制和释放记号包括文字和数字,在defense intelligence agency的手册DIAM 65-19 ,”安全传输记号(standard securitymarkings)”中定义。<br />
传输控制码Transmission Control Code (TCC 域): 24 位提供一种方法用来分隔信息交通流和在订户中定义利益控制实体(Provides a means to segregate traffic and define controlled          communities of interest among subscribers).TCC值是三字母词,
可以从HQ DCA Code 530获取。<br />
在分片时必须被拷贝。该选项在数据报中几乎都出现一次。<br />
宽松源站和记录路径(Loose Source and Record Route)<br />

```go
+ -------- + -------- + -------- + --------- // ------ -  + 
| 10000011 | 长度| 指针| 路线数据| 
+ -------- + -------- + -------- + --------- // -------- + 
    Type = 131    
```

松散源和记录路径(LSRR)选项提供了一种方法让internet数据报的源提供给网关在前推数据报到目的地时使用的路径信息,并且用来记录路径信息。<br />
该选项以选项类型码开始。第二个八位字节是包括选项类型码的选项长度。然后是length八位字节,pointer八位字节和length-3八位字节的路径信息。第三个八位字节是指向路径信息的指针,该路径信息指示由要被处理的下一个源地址开始。指针相对于这个选项,最小的合法值是4。路径数据由一系列internet地址组成。<br />
每个internet地址是4字节32位。如果指针大于长度,源路径是空(且记录路径满),则路径基于目的地址域。如果目的地址已经到达,且指针不大于长度,源路径中的下一个地址将代替目的地址中的地址域,记录的路径地址代替刚才使用的源地址,指针减4。<br />
记录路径地址(The recorded route address)是internet模块自己的地址,该地址是数据报前推的地址。<br />
以记录路径代替源路径的过程意味着选项(以及作为整体的IP头部)长度在数据报在internet上传送的时候保持不变这个选项是一个松散源路径,因为网关或者宿主IP都允许使用其它中继网关的任何路径到达路径中的下一个地址。<br />
在分片的时候必须被拷贝。在一个数据报中基本上出现一次。<br />
严格源站和记录路径(Strict Source and Record Route)<br />
```go         
+--------+--------+--------+---------//--------+        
|10001001| length | pointer|     route data    |        
+--------+--------+--------+---------//--------+         
Type=137         
```

严格的源和记录路径(SSRR)选项提供了一种让internet数据报的源提供了让网关在前推数据报到目的地时使用的路径的信息,以及记录路径信息的方法该选项以选项类型码开始。第二个八位字节是包括选项类型码的选项长度。然后是length八位字节,pointer八位字节和length-3八位字节的路径信息。第三个八位字节是指向路径信息的指针,该路径信息指示由要被处理的下一个源地址开始。指针相对于这个选项,最小的合法值是4。路径数据由一系列internet地址组成。每个internet地址是4字节32位。如果指针大于长度,源路径是空(且记录路径满),则路径基于目的地址域。如果目的地址已经到达,且指针不大于长度,源路径中的下一个地址将代替目的地址中的地址域,记录的路径地址代替刚才使用的源地址,指针减4。记录路径地址(The recorded route address)是internet模块自己的地址,该地址是数据报前推的地址。<br /> 
以记录路径代替源路径的过程意味着选项(以及作为整体的IP头部)长度在数据报在internet上传送的时候保持不变该选项十一个严格的源路径,因为网关或者宿主IP必须直接通过下一个地址中指示的互联的网络发送数据报给源路径里面的下一个地址,以到达在路径中指定的下一个网关或者主机。<br />
在分片的时候必须被拷贝。<br />
在一个数据报中基本上出现一次。<br />
记录路径(Record Route) <br />
```go
+--------+--------+--------+---------//--------+
|00000111| length | pointer|     route data    |        
+--------+--------+--------+---------//--------+         
 Type=7         
 ```
记录路径选项提供了一种记录一个internet数据报的路径的方法该选项以选项类型
码开始。<br />
第二个八位字节是包括选项类型码的选项长度。然后是length八位字节,pointer八位字节和length-3八位字节的路径信息。第三个八位字节是指向路径数据的指针,指示了开始下一个保存路径地址的区域的八位字节。<br />
指针是针对于该选项的,最小合法值是4。一个记录路径由一系列的internet地址组成。每个internet地址是32位4字节。<br />
如果指针大于长度,记录路径区域满,发起宿主必须将该选项和一个足够大的路径区域结婚起来以容纳所有的预期地址。选项的大小并不因为增加地址而改变。路径数据区域的初使内容必须是0。当internet模块为一个数据报选路的时候,它检查看记录路径选项是否存在。<br />
如果存在,它将自己的internet地址插入到记录路径中,该记录路径以指针指示
的八位字节开始,然后将指针加4。如果路径数据已经满了(指针超过了长度),数据报将前推,
且不再将地址插入到记录路径。如果还有空间,但空组不足于容纳要插入的整个地址,源数据报被认为是错误的,且将被丢弃。在上述两种情况下,一个ICMP参数问题信息可以被发送到源宿主。<br />
在分片时不拷贝,仅保留在第一个分片中。几乎在每个数据报中出现一次。
流标识(Stream Identifier)
```go
+--------+--------+--------+--------+        
|10001000|00000010|    Stream ID    |        
+--------+--------+--------+--------+         
Type=136 Length=4         
```
该选项提供了一种在不支持流概念的网络上<br />
携带16位SATNET流标识的方式必须在分片时被拷贝,在一个数据报中基本出现一次。<br />
Internet时间戳(Internet Timestamp)<br />
```go         
+--------+--------+--------+--------+        
|01000100| length | pointer|oflw|flg|        
+--------+--------+--------+--------+        
|         internet address          |
+--------+--------+--------+--------+        
|             timestamp             |        
+--------+--------+--------+--------+     
Type = 68
```
选项长度是计入类型,长度,指针和overflow/flag字节(最大长度40)的八位字节数目。指
针是从该选项开始到时间戳结束加1的八位字节数目(比如,它指向下一个时间戳的空间的开始字节)。<br />
最小合法值是5。当指针大于长度的时候,时间戳区域满。<br />
Overflow(oflw)[4位]是由于缺乏空间不能登记时间戳的IP模块的数目Flag(flg)[4字节]值是:<br />          
0 –仅是时间戳,以连续的32位字存储<br /> 
1 –每个时间戳先于登记实体的internet地址<br />
3 – internet地址预先设定。<br />
一个IP模块仅当他的地址同下一个指定的internet地址匹配的时候才登记时间戳时间戳是右对齐的(right-justified), 32位从UT子夜开始的32位毫秒如果时间不能以毫秒提供或者不能参照UT子夜提供,那么任何时间可以插入到时间戳中,将时间戳的高位字节设为1,来指示一个非标准值的使用。<br />
发起宿主必须将该选项同一个足够大的时间戳数据区域结合起来,以容纳预期的所有时间戳。选项大小不因为增加时间戳改变。时间戳数据区域的初使内容必须设置为0或者internet地址/0对。如果时间戳数据区域满了(指针超过了长度),数据报就前推,而不再插入时间戳,但overflow数目加1。<br />
如果还有空间但是不足于插入一个完整的时间戳,或者overflow数目自己溢出了,则原始数据报将被视为错误的而被丢弃。在上述两种情况下,一个ICMP参数问题消息可以被发送到源宿主。<br />
时间戳选项在分片时不被拷贝。它在第一个分片时携带。在每个数据报中基本出现一次。填充(Padding): 可变internet头部填充用来确保internet头部在32位边界结束。填充字节是0。<br />
### 3.2 讨论(Discussion) 协议的实现必须是健壮的。

每个协议的实现必须预期同其它实现交互操作。虽然本规范的目的是为了明确协议可能存在不同的解释。一般情况下,一个实现的发送行为必须是保守的,接收行为则是宽大(兼容)的。也就是说,一个实现必须小心发送正确格式的数据报,但是必须接收任何它可以解析的数据报(比如,不反对意义仍然清晰的技术错误)基本的internet服务是数据报驱动的,并且在网关提供了数据报的分片,以及在目的主机的internet模块上的重组。<br />
当然,在网络内部进行分片和重组,或者通过私底下约定在一个网络的网关之间进行分片和重组,是允许的,因为对internet协议和其它高层协议来说,这是透明的。这种透明类型的分片和重组称为“网络透明(network-dependent)”(或者intranet分片),不在此讨论。<br />
Internet地址在host级别区分了源地址和目的地址,且提供了协议域。假定在一个主机每个协议提供多路是必须的。<br />
寻址(Addressing)为了提供网络分配地址的自由性,以及允许大量的规模从小到中的网络,地址的解释如此编码以在少量的网络上提供大量的主机,数量中等的网络上提供数量中等的主机,大量的网络上提供数量较少的主机。<br />
另外,还有转义码用于扩展的编码模式地址格式(Address Formats):<br />  
     
```go
高阶位格式类
--------------- ------------------------------- ---- - 
0 7位网络，24位主机，
10 14位网络，16位主机b 
110 21位网络，8位主机c 
111转向扩展寻址模式
```
 在network域上的0值意味着这个网络。<br />
 这仅在某些ICMP消息中使用。扩展的地址模式还未定义。这些特性保留给以后用。为网络地址分配的真实值在”Assigned Numbers”[9]中给出。由局域网分配的局域网地址,必须允许一个单独的物理主机充当几个不同的internet主机。也就是说,在internet主机地址和网络/主机接口之间必须有一个映射,允许多个internet地址对应一个接口。同时,必须允许一个主机有多个物理地址,且每个从这些地址出来的数据报,必须被视为从一个单独的主机出来的。<br />   
Internet地址和ARPANET,SATNET,PRNET和其它网络之间的地址映射在”Address Mappings”[5]中进行描述。<br />
分片和重组internet标识头部(ID)同源地址,目的地址和协议头部一起在重组的时候来标识数据分片。如果数据报不是最后一个分片,设置More 分片标志位(MF)。分片偏移头部指示了分片位置,相对于初始的未分片的数据报的开始。分片以8位字节计数。未分片的数据报具有全0的分片信息(MF=0,分片偏移=0)。如果一个internet数据报被分片,它的数据部分必须在8位字节边界分隔。这种格式允许一个65536的八位字节数据报有2**13=8192个8位字节分片。注意这是同数据报总长度头部一致的(当然,头部是以总长度而不是分片来计算的)当分片发生的时候,有些选项被拷贝,但是其它的仍然仅在第一个分片中保留。每个internet 模块必须能够在不进行分片的情况下前推一个68个八位字节的数据报。这是因为一个internet头部可能要达到60个八位字节,而最小的分片是8个8位字节。每个internet目的地必须能够接受一个576个八位字节的数据报,不论这个数据报是在一个分片里面,还是在多个分片里面需要重组。<br />
可能被分片影响到的头部包括:<br />
(1)选项头部(options)<br />
(2)more分片标志<br />
(3)分片偏移<br />
(4)internet头部长度<br />
(5)总长度头部<br />
(6)头部校验和<br />
如果设置了禁止分片标志(DF)位,数据报的分片是不允许的,虽然它可能被丢弃。
这可以用在当接受主机没有足够资源进行重组数据报的情况下禁止分片。
一个禁止分片特性的例子是向一个小主机下线装载(down line load)。
一个小主机可以有一个boot捆绑程序,允许一个数据报存储在里面,然后执行它(Oneexample of use of theDon't Fragment feature is to down line load a small host. A small host could have a boot strap program   that accepts a datagram stores it in memory and then executes it.)分片和重组可能更容易通过例子描述。<br />
下面的程序是例子实现。<br />
在下面的伪程序中的通用符号:”=<”表示小于等于,”#”表示不等于,”=”表示等于,”<-“表示设置成,另外,“x to y”表示包含x排除y。<br />
比如4 to 7包括4,5,和6(不包括7)分片程序的一个例子:<br />
可以在下一个网络中被传输的最大数据报大小被称为最大传输单元(MTU)如果总长度小于或者等于最大传输单元,则在处理数据报的时候直接将数据报呈给下一个节点。<br />
否则将数据报分隔成两个分片,第一个分片设置成最大的分片大小,第二个分片则是数据报的剩余部分。第一个分片在数据报处理中被呈送给下一个节点,而第二个分片如果仍然比MTU大则被呈送给分片程序。<br />
符号(Notation):<br />
FO    - 分片偏移(Fragment Offset)<br />
IHL   - Internet头部长度(Internet Header Length)<br />
DF    - 禁止分片标志(Don't Fragment flag)<br />
MF    - More分片标志(More Fragments flag)<br />
TL    - 总长度(Total Length)<br />
OFO   - 老分片偏移(Old Fragment Offset)<br />
OIHL - 老Internet头部长度(Old Internet Header Length)<br />
OMF   - 老More分片标志(Old More Fragments flag)<br />
OTL   - 老总长度(Old Total Length)<br />
NFB   - 分片块数量(Number of Fragment Blocks)<br />
MTU   -  最大传输单元(Maximum Transmission Unit)<br />
程序(Procedure):<br />
如果TL<=MTU,则将该数据报上呈给下一个数据报处理环节否则如果DF=1,则丢弃数据报否则处理第一个分片:<br />
(1)拷贝原来的internet头部<br />
(2)OTHL=OHL;OTL=TL;OFO=FO;OMF=MF<br />
(3)NFB=(MTU-IHL*4)/8<br />
(4)附着上第一个NFB*8的数据块<br />
(5)修正头部MF=1;TL=(IHL*4)+(NFB*8);重新计算校验和<br />
(6)将该分片提交给数据报处理流程的下一个节点<br />
接着处理第二个分片:<br />
(7)选择性地拷贝internet头部(有些选项不拷贝,参见选项定义)<br />
(8)填充剩下的数据<br />
(9) 修正头部:<br />
IHL=(((OIHL*4)-(未拷贝的选项长度))+3)/4;<br />
TL=OTL-NFB*8-(OIHL-IHL)*4);<br />
FO=OFO+NFB;MF=OMF;<br />
重新计算校验和<br />
(10) 提交该数据分片给数据报处理流程中的下一个节点.完成在上述程序中,每个分片(除最后一个)被设置成最大允许大小。<br />
另外一个可选的方案是产生小于最大允许大小的分片。<br />
比如,分片程序可以重复地将大数据报分成两半直到最后的分片小于最大传输大小。<br />
一个重组程序例子<br />
对每个数据报来说,buffer标识是通过源地址,目的地址,协议和分片标识头部结合计算得到的。<br />
如果这是一个完整的数据报(也就是分片偏移和more标志头部都是0),则任何同这个buffer标识关联的重组资源被释放,数据报被前推给下一个节点。<br />
如果这个buffer标识现在没有其它分片,则重组资源被分配。重组资源包括一个数据缓冲,一个头部缓冲,一个分片块位表,一个总数据长度头部,和一个定时器。<br />
分片的数据根据分片偏移和长度被放在数据缓冲中,且在分片块位表中根据接收到的分片块设定位。<br />
如果是第一个分片(分片偏移为0),头部被放在头部缓冲中。<br />
如果是最后一个分片(more标志位是0),总的数据长度被计算。<br />
如果分片完成了数据报(通过检查设置在分片块表中的位来测试),则数据报被送到数据报的下一个处理步骤中。<br />
否则,定时器设置成当前定时器值和分片生存时间(TTL)两者的大值,然后重组程序放弃控制。<br />
如果定时器时间到了,该缓冲标识的所有的重组资源将被释放。定时器的初始化设置是比重组等待时间小的界限。<br />
这是因为如果到达分片的生存时间如果大于当前定时器值,等待时间将被增加,但是如果到达分片的生存时间小于当前定时器值,则不会被减少。该定时器可以达到的最大值是生存时间的最大值(大约4.25分钟左右)。当前推荐的初始化定时器设置是15秒。可以根据协议积聚的经验修改该值。注意该参数值的选择是同可用的缓冲容量以及传输媒介的数据传输率相关的。也就是说,数据传输率乘以定时器值等于缓冲大小(e.g.,10kb/s*15s=150kb).<br />
符号(Notation):<br />
FO    - 分片偏移(Fragment Offset)<br />
IHL   - Internet头部长度(Internet Header Length)<br />
MF    - More分片标志(More Fragments flag)<br />
TTL   - 生存时间(Time To Live)<br />
NFB   - 分片块数目(Number of Fragment Blocks)<br />
TL    - 总长度(Total Length)<br />
TDL   - 总数据长度(Total Data Length)<br />
BUFID - Buffer标识(Buffer Identifier)<br />
RCVBT - 分片接收位表(Fragment Received Bit Table)<br />
TLB   - 定时器下限(Timer Lower Bound)<br />
程序(Procedure):<br />
(1) BUFID <- source|destination|protocol|identification;<br />
(2) IF FO = 0 AND MF = 0<br />
(3)     THEN IF buffer with BUFID is allocated<br />
(4)             THEN flush all reassembly for this BUFID;<br />
(5)          Submit datagram to next step; DONE.<br />
(6)     ELSE IF no buffer with BUFID is allocated<br />
(7)             THEN allocate reassembly resources with BUFID;TIMER <- TLB; TDL <- 0;<br />
(8)         put data from fragment into data buffer with BUFID from octet FO*8 tooctet (TL-(IHL*4))+FO*8;<br />
(9)          set RCVBT bits from FO to FO+((TL-(IHL*4)+7)/8);<br />
(10)         IF MF = 0 THEN TDL <- TL-(IHL*4)+(FO*8)<br />
(11)         IF FO = 0 THEN put header in header buffer<br />
(12)         IF TDL # 0<br />
(13)          AND all RCVBT bits from 0 to (TDL+7)/8 are set <br />
(14)            THEN TL <- TDL+(IHL*4)<br />
(15)                 Submit datagram to next step;<br />
(16)                 free all reassembly resources for this BUFID; DONE.<br />
(17)         TIMER <- MAX(TIMER,TTL);<br />
(18)         give up until next fragment or timer expires;<br />
(19) timer expires: flush all reassembly with this BUFID; DONE.<br />
在这个例子中,两个或多于两个的分片包含了同样的数据。<br />
不管是同一个或者是局部的交迭,本程序使用在数据缓冲中最近到达的拷贝以及最近投递的数据报。<br />
标识(Identification)<br />
数据报标识的选择是基于提供一种唯一标识特殊数据报的分片的方式的需要。重组分片的协议模块在当分片具有相投的源地址,目的地址,协议和标识的时候,断定他们属于同一个数据报。因此,发送者必须选择一个在数据报(或者数据报的任一分片在internet生存期间对该源地址,目的地址对和协议来说是唯一的标识。<br />
发送协议模块需要维护一个标识表,对于同它进行通信的目的地址在最后一个的最大包生命周期内维护一个条目。<br />
但是,既然标识允许65536个不同的值,有些主机可用简单地使用独立于目的地址的唯一标识。<br />
由高层协议来选择标识是合理的。比如,TCP协议模块可能重传一个相同的TCP分片,如果重传携带了同第一次传送时一样的标识,正确接收的可能性会加强,因为他们中的任何一个数据报都可用用来构造一个正确的TCP分片。<br />
服务类型(Type of Service)<br />
服务类型(TOS)用于internet服务质量选择。服务类型通过一套参数(优先级,延迟,吞吐和可靠性)来指定。这套参数被映射成数据报传输中的特定网络的真实服务参数。<br />
优先级(Precedence). 数据报重要性的独立衡量。<br />
延迟(Delay). 立即投递对该指示的数据报是重要的。<br />
吞吐(Throughput). 高数据率对具有该指示的数据报是重要的。<br />
可靠性(Reliability). 高层确保投递可靠的努力对有该指示的数据报是重要的
比如,ARPANET有个优先级位,可以选择标准(“standard”)信息(type 0)和未受控制(“uncontrolled”)的信息(type 3)。单一包或者多包也可以视为一个服务参数。未受控制的信息倾向于以较低的可靠性投递,但是可以获得比较低的延迟。假设一个internet数据报要在ARPANET上传送,设置internet服务类型为:<br />
优先级(Precedence):    5<br />
延迟(Delay):         0<br />
吞吐(Throughput):    1<br />
可靠性(Reliability):   1<br />
在这个例子中,这些参数向ARPANET上可用参数的映射就是设置ARPANET优先级位(priority bit)为打开,因为Internet优先级(precedence)处于它的优先级区域的上半部。选择标准(standard)消息,因为有吞吐和可靠性要求指示,二没有延迟指示。更详细的信息在”Service Mappings”[8]的服务映射中给出。<br />
生存时间(Time to Live)<br />
生存时间由发送者设置成允许数据报在internet系统上允许存活的最大时间。如果数据报在internet 系统上长于生存时间,则数据报必须被销毁。<br />
在internet头部被处理的每个点,该头部必须减小,以反应花在处理数据报上的时间。即使无法获得实际花费时间的本地信息,该头部也必须减1。时间以秒单位衡量(比如,值1标识1秒)。因此,最大生存时间是255秒或者4.25分钟。由于处理数据报的每个模块至少对TTL减1,即使他在小于1秒内处理完数据报,因此TTL只能被当作数据报可以存在的时间上限。<br />
有些高层可靠性连接协议是基于老的数据报副本在一定时间间隔之后不会再到达的假设。TTL给了这些协议假设满足的保证。<br />
选项(Options)<br />
选项对每个数据报是可选的,但是对实现是要求的。也就是说,选项的出现与否是发送者的选择,但是每个internet模块必须能够解析每个选项。在选项头部可以有多个选项。<br />
选项可以不结束于32位边界。Internet头部必须以0字节填充。这些字节的开始将被解析成选项结束标志,其它的结识乘internet头部填充。<br />
每个internet模块必须能够作用于每个选项。如果分类的、限制的、分隔的通信流要传送,安全选项是需要的。<br />
校验和(Checksum)<br />
如果internet头部改变,internet头部校验和要重新计算。比如,生存时间的减少,internet选项的增加或者变化,或者由于分片。在internet级别的这个校验和用来防止internet头部的传输错误。<br />
有些应用程序允许一部分数据位错误,但是不允许重传延迟。如果internet协议强迫数据的正确性,则这些程序不能支持。<br />
错误(Errors)<br />
internet协议错误可以通过ICMP信息报告。<br />
### 3.3. 接口(Interfaces)
IP的用户接口的功能性描述最好是功能性的,因为每个操作系统有不同的库(facility)。因此,我们要警告读者,不同的IP实现可能有不同的用户接口。<br />
但是,所有的IPs实现必须提供一套最小的服务以确保所有的ip实现能够职称同样的协议层次结构。这一章指定了所有的ip实现要求的功能接口。<br />
Internet协议接口一边是局域网络,另一边是较高层的协议或者一个应用程序。下面,高层协议或者应用程序将称为用户,因为它正在使用internet模块。因为internet协议是一个数据报协议,在数据报传输中,维护了最小内存或者状态,每个调用internet协议模块的用户提供IPs所需的全部信息以执行所需服务。<br />
一个上层接口的例子<br />
下面两个例子调用满足了internet模块通信的用户要求(”=>”标识返回):<br />
SEND (src, dst, prot, TOS, TTL, BufPTR, len, Id, DF, opt => result)<br />
where:<br />
src = source address(源地址)<br />
dst = destination address(目的地址)<br />
prot = protocol(协议)<br />
TOS = type of service(服务类型)<br />
TTL = time to live(生存实际那)<br />
BufPTR = buffer pointer(缓冲指针)<br />
len = length of buffer(缓冲长度)<br />
Id = Identifier(标识)<br />
DF = Don't Fragment(禁止分片)<br />
opt = option data(选项数据)<br />
result = response(响应)<br />
OK = datagram sent ok(数据报发送OK)<br />
Error = error in arguments or local network error(参数或者局域网络错误)<br />
注意优先级包括在TOS里面,安全(security)/间隔(compartment)在选项(option)中传递 RECV (BufPTR, prot, => result, src, dst, TOS, len, opt)<br />
where:<br />
BufPTR = buffer pointer(缓冲指针)<br />
prot = protocol(协议)<br />
result = response(响应)<br />
OK = datagram received ok(数据报接收OK)<br />
Error = error in arguments(参数错误)<br />
len = length of buffer(缓冲长度)<br />
src = source address(源地址)<br />
dst = destination address(目的地址)<br />
TOS = type of service(服务类型)<br />
opt = option data(选项数据)<br />
当用户发送一个数据报的时候,它提供所有的参数,执行SEND调用。Internet模块在收到该调用以后,检查参数,并准备和发送消息。如果参数没有问题,且数据报被局域网接受,调用成功返回。如果参数不合法,或者数据报不被局域网络接受,调用返回失败。失败返回的时候,必须生成问题原因的合理报告,但是这种报告的细节由各自的实现决定。<br />
当一个数据报从局域网络到达internet协议模块的时候,队列中可能有从用户指定地址来的一个RECV调用,或者没有,在第一种情况中,在队列中的的调用通过传递从数据报到用户的信息而被满足。在第二种情况下,指定地址的用户被通知有悬而未决的数据报。如果指定的用户地址不存在,一个ICMP错误信息返回给发送者,数据被丢弃。<br />
用户的通知可能通过一个伪中断或者其它相同的机制,一个适合于实现的特殊操作系统环境的机制。<br />
用户的接收调用可以被一个悬而未决的数据报立即满足,或者调用被悬置,直到数据报到达。<br />
在发送主机有多个地址(多个物理连接或者逻辑地址)的情况下,源地址包含在发送调用中。Internet模块必须检查确保源地址是该主机的合法地址之一。<br />
协议可以允许或者要求一个到internet的调用来指示某一类型数据报的兴趣或者保留排他性的使用。<br />
这一节从功能的角度出发阐述了用户/IP(USER/IP)接口。使用的名词同在高层语言的函数调用中的大多数程序一样,但这种用法并被意味着排除trap类型服务调用(比如,SVCs,UUOs,EMTs)或者其它任何形式的进程间通信。<br />

## 附录A:

### 例子 1:
下面是internet数据报携带的最小数据的例子:<br />
```go
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Ver= 4 |IHL= 5 |Type of Service|        Total Length = 21      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Identification = 111     |Flg=0|   Fragment Offset = 0   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   Time = 123 | Protocol = 1 |        header checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         source address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      destination address                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     data      |
+-+-+-+-+-+-+-+-+
```
<center>
Example Internet Datagram<br />
Figure 5.</center>
注:每个小间隔表示一个位。这是internet协议版本4的一个数据报。Internet头部包含5个32位字,数据报的总长度是21个八位字节。这个数据报是一个完整的数据报(不是分片)<br />

### 例子2:
在这个例子中,我们首先看到一个中等大小的internet数据报(452数据字节),然后是两个internet 分片,可能是当该数据报在最大允许传输代谢哦啊是280字节的网络上传输时该数据报的分片。<br />
```go
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|Ver= 4 |IHL= 5 |Type of Service|       Total Length = 472      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|     Identification = 111      |Flg=0|     Fragment Offset = 0 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|   Time = 123 | Protocol = 6 |        header checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                         source address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                      destination address                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                             |
\                                                               \
\                                                               \
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|             data              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```
Example Internet Datagram
Figure 6.
Now the first fragment that results from splitting the datagram after
256 data octets.
```go
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|Ver= 4 |IHL= 5 |Type of Service|       Total Length = 276      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|     Identification = 111      |Flg=1|     Fragment Offset = 0 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|   Time = 119 | Protocol = 6 |        Header Checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                         source address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                      destination address                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
\                                                               \
\                                                               \
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                        Example Internet Fragment
```
Figure 7.
[Page 36]
September 1981
Internet Protocol
And the second fragment.
```go
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|Ver= 4 |IHL= 5 |Type of Service|       Total Length = 216      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|     Identification = 111      |Flg=0| Fragment Offset = 32 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|   Time = 119 | Protocol = 6 |        Header Checksum        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                         source address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                      destination address                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
\                                                               \
\                                                               \
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|            data               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
Example Internet Fragment
```

Figure 8.
[Page 37]
September 1981
Internet Protocol
### 例子 3:
Here, we show an example of a datagram containing options:
```go
0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|Ver= 4 |IHL= 8 |Type of Service|       Total Length = 576      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|       Identification = 111    |Flg=0|     Fragment Offset = 0 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|   Time = 123 | Protocol = 6 |       Header Checksum         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                        source address                         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                      destination address                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
| Opt. Code = x | Opt. Len.= 3 | option value | Opt. Code = x |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
| Opt. Len. = 4 |           option value        | Opt. Code = 1 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
| Opt. Code = y | Opt. Len. = 3 | option value | Opt. Code = 0 |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
\                                                               \
\                                                               \
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+    
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                        Example Internet Datagram
Figure 9.
```

<hr />