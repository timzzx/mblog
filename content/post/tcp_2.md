---
title: "TCP/IP学习理解(二)"
date: 2018-07-13T11:25:28+08:00
categories: [TCP/IP]
tags: [2018-07]
gitment: true
TableOfContents: true
---
>名词解释

# 使用Wiresharkz抓包工具时，发现有很多协议（protocol）

## ARP
* ARP是通过网络地址(例：IPv4)来定位MAC地址 (也称为以太网地址)。 ARP已经在很多网路层和数据链接层之间得以实现，包括IPv4，Chaosnet, DECnet和Xerox PARC Universal Packet (PUP) 使用IEEE 802标准, 光纤分布式数据接口, X.25, 帧中继和异步传输模式 (ATM)， IEEE 802.3和IEEE 802.11标准上IPv4占了多数流量。

## BROWSER
* CIFS浏览器协议定义由服务器发送和接收的消息，该服务器充当网络上可用服务的交换中心，正在进行网络上可用的打印或文件共享等服务的服务器，以及请求详细信息的客户端。特别的服务。

## DHCPv6
* 一个用来配置工作在IPv6网络上的IPv6主机所需的IP地址、IP前缀和/或其他配置的网络协议。

## DNS
* 域名系统（英文：Domain Name System，缩写：DNS）是互联网的一项服务。它作为将域名和IP地址相互映射的一个分布式数据库，能够使人更方便地访问互联网。DNS使用TCP和UDP端口53[1]。当前，对于每一级域名长度的限制是63个字符，域名总长度则不能超过253个字符。

## HTTP
* 超文本传输协议（英文：HyperText Transfer Protocol，缩写：HTTP）是一种用于分布式、协作式和超媒体信息系统的应用层协议[1]。HTTP是万维网的数据通信的基础。

## ICMPv6
* 是互联网控制消息协议（ICMP）在IPv6协议下的新版本。它在 RFC 4443 中被定义，其协议号（IPv6的Next Header域）为58。
* ICMPv6协议定义在TCP/IP参考模型中的网络层（Internet Layer），用于报告IPv6节点数据包处理过程中的错误消息和完成一些网络诊断功能（如ping和Traceroute等）。它是IPv6体系总体的的一个组成部分，其基本的协议[1]必须被所有IPv6实现和IPv6节点所完整支持。

## IGMPv3
* IGMP 是Internet Group Management Protocol（互联网组管理协议）的简称。它是TCP/IP 协议族中负责IP 组播成员管理的协议，用来在IP主机和与其直接相邻的组播路由器之间建立、维护组播组成员关系。 

## LLDP
* 一种数据链路层协议，网络设备可以通过在本地网络中发送LLPDU（Link Layer Discovery Protocol Data Unit）来通告其他设备自身的状态。是一种能够使网络中的设备互相发现并通告状态、交互信息的协议。

## LLMNR
* 在DNS 服务器不可用时，DNS 客户端计算机可以使用本地链路多播名称解析 (LLMNR—Link-Local Multicast Name Resolution)（也称为多播 DNS 或 mDNS）来解析本地网段上的名称。例如，如果路由器出现故障，从网络上的所有 DNS 服务器切断了子网，则支持 LLMNR 的子网上的客户端可以继续在对等基础上解析名称，直到网络连接还原为止。

## NBNS
* NBNS（网络基本输入/输出系统 (NetBIOS) 名称服务器）在基于 NetBIOS 名称访问的网络上提供主机名和地址映射方法。
* NetBIOS是Network Basic Input/Output System的简称，一般指用于局域网通信的一套API，不支持路由，NetBIOS提供三种不同的服务： 名字服务：名字登记和解析；会话服务：可靠的基于连接的通信；数据包服务：不可靠的无连接通信 。在Windows下通过nbtstat -n可以查询本地NetBIOS相关信息。
* NBNS是动态DNS的一种，Microsoft的NBNS实现称为WINS（Windows Internet Name Service）。WINS数据库是动态更新的。计算机每当初始化TCP/IP后都会将它的NetBIOS名和IP地址的对应关系映射到WINS服务器的数据库中。如果在局域网络中抓取并观察数据包，会发现有大量的NBNS数据包。开启了WINS的主机就会发出广播，使用UDP协议，连137端口。
* 如果计算机需要知道一个NetBIOS名称或者域名对应的IP地址，首先会查找本地Hosts文件和NetBIOS缓存，其次会向WINS服务器发出请求，会发出NBNS数据包，最后会联系DNS服务器进行解析。

## SMB Mailslot
* MB(全称是Server Message Block)是一个协议名，它能被用于Web连接和客户端与服务器之间的信息沟通。SMB最初是IBM的贝瑞·费根鲍姆（Barry Feigenbaum）研制的，其目的是将DOS操作系统中的本地文件接口“中断13”改造为网络文件系统。
* 公司的网络硬盘使用的协议

## SSDP
* 构成UPnP（通用即插即用）技术的核心协议之一。它为网络客户端（network client）提供了一种发现网络服务（network services）的机制，采用基于通知和发现路由的多播方式实现。
* SSDP客户端向此地址发送HTTP UDP 发现请求，查询某种类型的服务。SSDP服务在此地址上监听服务发现请求。当服务监听到的HTTP UDP 发现请求和它自己提供的服务匹配时，它以单播方式发送HTTP UDP 响应。

## SSL
* SSL(Secure Sockets Layer 安全套接层),及其继任者传输层安全（Transport Layer Security，TLS）是为网络通信提供安全及数据完整性的一种安全协议。TLS与SSL在传输层对网络连接进行加密。

## STP
* STP（Spanning Tree Protocol）是生成树协议的英文缩写。该协议可应用于在网络中建立树形拓扑，消除网络中的环路，并且可以通过一定的方法实现路径冗余，但不是一定可以实现路径冗余。生成树协议适合所有厂商的网络设备，在配置上和体现功能强度上有所差别，但是在原理和应用效果是一致的。

## TCP
* TCP（Transmission Control Protocol 传输控制协议）是一种面向连接的、可靠的、基于字节流的传输层通信协议，由IETF的RFC 793定义。在简化的计算机网络OSI模型中，它完成第四层传输层所指定的功能，用户数据报协议（UDP）是同一层内 [1]  另一个重要的传输协议。在因特网协议族（Internet protocol suite）中，TCP层是位于IP层之上，应用层之下的中间层。不同主机的应用层之间经常需要可靠的、像管道一样的连接，但是IP层不提供这样的流机制，而是提供不可靠的包交换。

## TLSv1.2
* 传输层安全性协议（英语：Transport Layer Security，缩写作 TLS），及其前身安全套接层（Secure Sockets Layer，缩写作 SSL）是一种安全协议，目的是为互联网通信，提供安全及数据完整性保障。网景公司（Netscape）在1994年推出首版网页浏览器，网景导航者时，推出HTTPS协议，以SSL进行加密，这是SSL的起源。IETF将SSL进行标准化，1999年公布第一版TLS标准文件。随后又公布RFC 5246 （2008年8月）与 RFC 6176 （2011年3月）。在浏览器、电子邮件、即时通信、VoIP、网络传真等应用程序中，广泛支持这个协议。主要的网站，如Google、Facebook等也以这个协议来创建安全连接，发送数据。目前已成为互联网上保密通信的工业标准。

## UDP
* UDP 是User Datagram Protocol的简称， 中文名是用户数据报协议，是OSI（Open System Interconnection，开放式系统互联） 参考模型中一种无连接的传输层协议，提供面向事务的简单不可靠信息传送服务，IETF RFC 768是UDP的正式规范。UDP在IP报文的协议号是17。
* UDP协议全称是用户数据报协议 [1]  ，在网络中它与TCP协议一样用于处理数据包，是一种无连接的协议。在OSI模型中，在第四层——传输层，处于IP协议的上一层。UDP有不提供数据包分组、组装和不能对数据包进行排序的缺点，也就是说，当报文发送之后，是无法得知其是否安全完整到达的。UDP用来支持那些需要在计算机之间传输数据的网络应用。包括网络视频会议系统在内的众多的客户/服务器模式的网络应用都需要使用UDP协议。UDP协议从问世至今已经被使用了很多年，虽然其最初的光彩已经被一些类似协议所掩盖，但是即使是在今天UDP仍然不失为一项非常实用和可行的网络传输层协议。
* 与所熟知的TCP（传输控制协议）协议一样，UDP协议直接位于IP（网际协议）协议的顶层。根据OSI（开放系统互连）参考模型，UDP和TCP都属于传输层协议。UDP协议的主要作用是将网络数据流量压缩成数据包的形式。一个典型的数据包就是一个二进制数据的传输单位。每一个数据包的前8个字节用来包含报头信息，剩余字节则用来包含具体的传输数据。

![TCP/IP](https://user-images.githubusercontent.com/8891076/42679961-34e68d1c-86b6-11e8-99c4-cc6c7a816894.jpg)