---
title: "TCP/IP学习理解(十)"
date: 2018-07-23T14:23:53+08:00
categories: [TCP/IP]
tags: [2018-07]
gitment: true
TableOfContents: true
---
# TCP：传输控制协议

>尽管TCP和UDP都使用相同的网络层（IP），TCP却向应用层提供与UDP完全不同的服务。TCP提供一种面向连接的、可靠的字节流服务。<br />
>面向连接意味着两个使用TCP的应用（通常是一个客户和一个服务器）在彼此交换数据之前必须先建立一个TCP连接。这一过程与打电话很相似，先拨号振铃，等待对方摘机说“喂”，然后才说明是谁。在第18章我们将看到一个TCP连接是如何建立的，以及当一方通信结束后如何断开连接。

## TCP通过下列方式来提供可靠性：

1.应用数据被分割成TCP认为最适合发送的数据块。这和UDP完全不同，应用程序产生的数据报长度将保持不变。由TCP传递给IP的信息单位称为报文段或段（segment）（参见图1-7）。在18.4节我们将看到TCP如何确定报文段的长度。<br />
2.当TCP发出一个段后，它启动一个定时器，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重发这个报文段。在第21章我们将了解TCP协议中自适应的超时及重传策略。<br />
3.当TCP收到发自TCP连接另一端的数据，它将发送一个确认。这个确认不是立即发送，通常将推迟几分之一秒，这将在19.3节讨论。<br />
4.TCP将保持它首部和数据的检验和。这是一个端到端的检验和，目的是检测数据在传输过程中的任何变化。如果收到段的检验和有差错，TCP将丢弃这个报文段和不确认收到此报文段（希望发端超时并重发）。<br />
5.既然TCP报文段作为IP数据报来传输，而IP数据报的到达可能会失序，因此TCP报文段的到达也可能会失序。如果必要，TCP将对收到的数据进行重新排序，将收到的数据以正确的顺序交给应用层。<br />
6.既然IP数据报会发生重复，TCP的接收端必须丢弃重复的数据。<br />
7.TCP还能提供流量控制。TCP连接的每一方都有固定大小的缓冲空间。TCP的接收端只允许另一端发送接收端缓冲区所能接纳的数据。这将防止较快主机致使较慢主机的缓冲区溢出。<br />

## TCP的首部
<center><table> <tr> <td>IP首部 </td> <td>TCP首部 </td> <td>TCP数据 </td> </tr> </table></center>
<br />

<center><table>  <tr> <td colspan="16">源端口 </td> <td colspan="16">目标端口 </td> </tr> <tr> <td colspan="32">32位序号 </td> </tr> <tr> <td colspan="32">32位确认序号 </td> </tr> <tr> <td colspan="4">4位首部长度 </td> <td colspan="6">保留（6位） </td> <td colspan="1">URG </td> <td colspan="1">ACK </td> <td colspan="1">PSH </td> <td colspan="1">PST </td> <td colspan="1">SYN </td> <td colspan="1">FIN </td> <td colspan="16">16位窗口大小 </td> </tr> <tr> <td colspan="16">16位检验和 </td> <td colspan="16">16位紧急指针 </td> </tr> <tr> <td colspan="32">选项 </td> </tr> <tr> <td colspan="32">数据 </td> </tr> </table></center>

<b>源端口、目标端口：</b>计算机上的进程要和其他进程通信是要通过计算机端口的，而一个计算机端口某个时刻只能被一个进程占用，所以通过指定源端口和目标端口，就可以知道是哪两个进程需要通信。源端口、目标端口是用16位表示的，可推算计算机的端口个数为2^16个。<br /><br />
<b>序列号：</b>表示本报文段所发送数据的第一个字节的编号。在TCP连接中所传送的字节流的每一个字节都会按顺序编号。由于序列号由32位表示，所以每2^32个字节，就会出现序列号回绕，再次从 0 开始。那如何区分两个相同序列号的不同TCP报文段就是一个问题了，后面会有答案，暂时可以不管。<br /><br />
<b>确认号：</b>表示接收方期望收到发送方下一个报文段的第一个字节数据的编号。也就是告诉发送发：我希望你（指发送方）下次发送的数据的第一个字节数据的编号是这个确认号。也就是告诉发送方：我希望你（指发送方）下次发送给我的TCP报文段的序列号字段的值是这个确认号。<br /><br />
<b>TCP首部长度：</b>由于TCP首部包含一个长度可变的选项部分，所以需要这么一个值来指定这个TCP报文段到底有多长。或者可以这么理解：就是表示TCP报文段中数据部分在整个TCP报文段中的位置。该字段的单位是32位字，即：4个字节。<br /><br />
<b>URG：</b>表示本报文段中发送的数据是否包含紧急数据。URG=1，表示有紧急数据。后面的紧急指针字段只有当URG=1时才有效。<br /><br />
<b>ACK：</b>表示是否前面的确认号字段是否有效。ACK=1，表示有效。只有当ACK=1时，前面的确认号字段才有效。TCP规定，连接建立后，ACK必须为1。<br /><br />
<b>PSH：</b>告诉对方收到该报文段后是否应该立即把数据推送给上层。如果为1，则表示对方应当立即把数据提交给上层，而不是缓存起来。<br /><br />
<b>RST：</b>只有当RST=1时才有用。如果你收到一个RST=1的报文，说明你与主机的连接出现了严重错误（如主机崩溃），必须释放连接，然后再重新建立连接。或者说明你上次发送给主机的数据有问题，主机拒绝响应。<br /><br />
<b>SYN：</b>在建立连接时使用，用来同步序号。当SYN=1，ACK=0时，表示这是一个请求建立连接的报文段；当SYN=1，ACK=1时，表示对方同意建立连接。SYN=1，说明这是一个请求建立连接或同意建立连接的报文。只有在前两次握手中SYN才置为1。<br /><br />
<b>FIN：</b>标记数据是否发送完毕。如果FIN=1，就相当于告诉对方：“我的数据已经发送完毕，你可以释放连接了”<br /><br />
<b>窗口大小：</b>表示现在运行对方发送的数据量。也就是告诉对方，从本报文段的确认号开始允许对方发送的数据量。<br /><br />
<b>校验和：</b>提供额外的可靠性。具体如何校验，参考其他资料。<br /><br />
<b>紧急指针：</b>标记紧急数据在数据字段中的位置。<br /><br />
<b>选项部分：</b>其最大长度可根据TCP首部长度进行推算。TCP首部长度用4位表示，那么选项部分最长为：(2^4-1)*4-20=40字节。<br /><br />
<b>选项部分的应用：</b><br /><br />
<b>MSS最大报文段长度(Maxium Segment Size)：</b>指明数据字段的最大长度，数据字段的长度加上TCP首部的长度才等于整个TCP报文段的长度。MSS值指示自己期望对方发送TCP报文段时那个数据字段的长度。通信双方可以有不同的MSS值。如果未填写，默认采用536字节。MSS只出现在SYN报文中。即：MSS出现在SYN=1的报文段中。<br /><br />
<b>窗口扩大选项(Windows Scaling)：</b>由于TCP首部的窗口大小字段长度是16位，所以其表示的最大数是65535。但是随着时延和带宽比较大的通信产生（如卫星通信），需要更大的窗口来满足性能和吞吐率，所以产生了这个窗口扩大选项。<br /><br />
<b>SACK选择确认项(Selective Acknowledgements)：</b>用来确保只重传缺少的报文段，而不是重传所有报文段。比如主机A发送报文段1、2、3，而主机B仅收到报文段1、3。那么此时就需要使用SACK选项来告诉发送方只发送丢失的数据。那么又如何指明丢失了哪些报文段呢？使用SACK需要两个功能字节。一个表示要使用SACK选项，另一个指明这个选项占用多少字节。描述丢失的报文段2，是通过描述它的左右边界报文段1、3来完成的。而这个1、3实际上是表示序列号，所以描述一个丢失的报文段需要64位即8个字节的空间。那么可以推算整个选项字段最多描述(40-2)/8=4个丢失的报文段。<br /><br />
<b>时间戳选项（Timestamps）：</b>可以用来计算RTT(往返时间)，发送方发送TCP报文时，把当前的时间值放入时间戳字段，接收方收到后发送确认报文时，把这个时间戳字段的值复制到确认报文中，当发送方收到确认报文后即可计算出RTT。也可以用来防止回绕序号PAWS，也可以说可以用来区分相同序列号的不同报文。因为序列号用32为表示，每2^32个序列号就会产生回绕，那么使用时间戳字段就很容易区分相同序列号的不同报文。<br /><br />
<b>NOP(NO-Operation)：</b>它要求选项部分中的每种选项长度必须是4字节的倍数，不足的则用NOP填充。同时也可以用来分割不同的选项字段。如窗口扩大选项和SACK之间用NOP隔开。<br /><br />
<hr />

# TCP连接的建立与终止

1，TCP连接的建立<br /><br />
（1）服务器必须准备好接受外来的连接，通常通过socket，bind和listen这3个函数完成，称之为被动打开<br /><br />

（2）客户端通过connect发起主动打开，客户端TCP发送一个SYN分节，告诉服务器端客户将在连接中发送的数据的初始序列号，通常SYN分节不携带数据<br /><br />

（3）服务器端必须确认（ACK）客户端的SYN，同时自己发送一个SYN分节，它包含服务端在同一链接中发送的数据的初始序列号。服务端在单个分节中发送SYN和对客户端SYN的ACK（确认）<br /><br />

（4）客户端必须确认服务端的SYN（发送一个ACK进行确认）<br /><br />

这个交换至少需要3个分组，以上称之为TCP连接建立的三次握手<br /><br />

2，TCP连接的终止<br /><br />
（1）某个应用进程首先close，执行主动关闭。客户端的TCP发送一个FIN分节<br /><br />

（2）接收到这个FIN的服务端执行被动关闭。这个FIN由TCP确认。<br /><br />

（3）一段时间后，接收这个文件结束符的应用进程将调用close关闭它的套接字，这就导致它的TCP也发送一个FIN。<br /><br />

（4）接收这个最终的FIN的原发端TCP（即执行主动关闭的一段）确认这个FIN<br /><br />

既然每个方向都要发送一个FIN和ACK，通常情况下4个分节，某些情况下步骤1的FIN随数据一起发送，另外步骤2和3发送的分节也有可能合并成一个分节。<br /><br />

<hr />

# TCP的交互数据流

对于交互性要求比较高的应用,TCP 给出两个策略来提高发送效率和减低网络负担:<br /><br />
1. 捎带 ACK。
2. Nagle 算法(一次尽量多 的发数据)。

通常,在网络速度很快的情况下,比如用 lo 接口进行 telnet 通信,当按下字母键并要求回显的时候,客户端和服务器将经 历 发送按键数据->服务器发送按键数据的 ack -> 服务器端发送回显数据->客户端发送回显数据的 ACK 的过程,而其中的数据流量将 是40bit + 41bit+41bit+40bit = 162bit,如果在广域网里面,这种小分组的 TCP 流量将会造成很大的网络负担。<br /><br />

1.1捎带 ACK 的发送方式<br /><br />

这个策略是说,当主机收到远程主机的 TCP 数据报之后,通常不马上发送 ACK 数据报,而是等上一个短暂的时间,如果这段时 间里面主机还有发送到远程 主机的 TCP 数据报,那么就把这个 ACK 数据报“捎带”着发送出去,把本来两个 TCP 数据报整合成一个发 送。一般的,这个时间是200ms。可以明显地看 到这个策略可以把 TCP 数据报的利用率提高很多。<br /><br />

1.2Nagle 算法<br /><br />

上过 bbs 的人应该都会有感受,就是在网络慢的时候发贴,有时键入一串字符串以后,经过一段时间,客户端“发疯”一样突然回 显出很多内容,就好像数据一下子传过来了一样,这就是 Nagle 算法的作用。<br /><br />

Nagle 算法是说,当主机 A 给主机 B 发送了一个 TCP 数据报并进入等待主机 B 的 ACK 数据报的状态时,TCP 的输出缓冲区里面 只能有一个 TCP 数 据报,并且,这个数据报不断地收集后来的数据,整合成一个大的数据报,等到 B 主机的 ACK 包一到,就把这些 数据“一股脑”的发送出去。虽然这样的描述有些 不准确,但还算形象和易于理解,我们同样可以体会到这个策略对于低减网络负担的 好处。<br /><br />

在编写插口程序的时候,可以通过 TCP_NODELAY 来关闭这个算法。并且,使用这个算法看情况的,比如基于 TCP 的 X 窗口协议,如果处理鼠标事件时还是用这个算法,那么“延迟”可就非常大了。<br /><br />
<hr />

# TCP的成块数据流

对于 FTP 这样对于数据吞吐量有较高要求的要求,将总是希望每次尽量多的发送数据到对方主机,就算是有点“延迟”也无所谓。 TCP 也提供了一整套的策略来支持这样的需求。TCP 协议中有16个 bit 表示“窗口”的大小,这是这些策略的核心。<br /><br />

2.1传输数据时 ACK 的问题<br /><br />

在解释滑动窗口前,需要看看 ACK 的应答策略,一般来说,发送端发送一个 TCP 数据报,那么接收端就应该发送一个 ACK 数 据报。但是事实上却不是这 样,发送端将会连续发送数据尽量填满接受方的缓冲区,而接受方对这些数据只要发送一个 ACK 报文来 回应就可以了,这就是 ACK 的累积特性,这个特性大大减 少了发送端和接收端的负担。<br /><br />

2.2滑动窗口<br /><br />

滑动窗口本质上是描述接受方的 TCP 数据报缓冲区大小的数据,发送方根据这个数据来计算自己最多能发送多长的数据。如果发 送方收到接受方的窗口大小 为0的 TCP 数据报,那么发送方将停止发送数据,等到接受方发送窗口大小不为0的数据报的到来。书中的 P211和 P212很好的解释了这一点。<br /><br />

关于滑动窗口协议,书上还介绍了三个术语,分别是:<br /><br />
窗口合拢: 当窗口从左边向右边靠近的时候,这种现象发生在数据被发送和确认的时候。<br /><br />
窗口张开: 当窗口的右边沿向右边移动的时候,这种现象发生在接受端处理了数据以后。<br /><br />
窗口收缩: 当窗口的右边沿向左边移动的时候,这种现象不常发生。<br /><br />

TCP 就是用这个窗口,慢慢的从数据的左边移动到右边,把处于窗口范围内的数据发送出去(但不用发送所有,只是处于窗口内的数据可以发送。)。这就 是窗口的意义。图 20-6 解释了这一点。窗口的大小是可以通过 socket 来制定的,4096并不是最理想的窗口大 小,而16384则可以使吞吐量大大的 增加。<br /><br />

2.3数据拥塞<br /><br />

上面的策略用于局域网内传输还可以,但是用在广域网中就可能会出现问题,最大的问题就是当传输时出现了瓶颈(比如说一定 要经过一个 slip 低速链 路)所产生的大量数据堵塞问题(拥塞),为了解决这个问题,TCP 发送方需要确认连接双方的线路的数据最 大吞吐量是多少。这,就是所谓的拥塞窗口。<br /><br />

拥塞窗口的原理很简单,TCP 发送方首先发送一个数据报,然后等待对方的回应,得到回应后就把这个窗口的大小加倍,然后连 续发送两个数据报,等到对 方回应以后,再把这个窗口加倍(先是2的指数倍,到一定程度后就变成现行增长,这就是所谓的慢启动), 发送更多的数据报,直到出现超时错误,这样,发送端 就了解到了通信双方的线路承载能力,也就确定了拥塞窗口的大小,发送方就 用这个拥塞窗口的大小发送数据。要观察这个现象是非常容易的,我们一般在下载数据 的时候,速度都是慢慢“冲起来的”。<br /><br />
<hr />

# TCP的超时与重传

TCP协议是一种面向连接的可靠的传输层协议，它保证了数据的可靠传输，对于一些出错，超时丢包等问题TCP设计的超时与重传机制。其基本原理：在发送一个数据之后，就开启一个定时器，若是在这个时间内没有收到发送数据的ACK确认报文，则对该报文进行重传，在达到一定次数还没有成功时放弃并发送一个复位信号。 <br /><br />
　　这里比较重要的是重传超时时间，怎样设置这个定时器的时间（RTO），从而保证对网络资源最小的浪费。因为若RTO太小，可能有些报文只是遇到拥堵或网络不好延迟较大而已，这样就会造成不必要的重传。太大的话，使发送端需要等待过长的时间才能发现数据丢失，影响网络传输效率。 <br /><br />
　　由于不同的网络情况不一样，不可能设置一样的RTO，实际中RTO是根据网络中的RTT（传输往返时间）来自适应调整的。具体关系参考相关算法。<br /><br />
TCP慢启动<br /><br />

　　慢启动是TCP的一个拥塞控制机制，慢启动算法的基本思想是当TCP开始在一个网络中传输数据或发现数据丢失并开始重发时，首先慢慢的对网路实际容量进行试探，避免由于发送了过量的数据而导致阻塞。 <br /><br />
　　慢启动为发送方的TCP增加了另一个窗口：拥塞窗口(congestion window)，记为cwnd。当与另一个网络的主机建立TCP连接时，拥塞窗口被初始化为 1个报文段（即另一端通告的报文段大小）。每收到一个ACK，拥塞窗口就增加一个报文段（cwnd以字节为单位，但是慢启动以报文段大小为单位进行增加）。发送方取拥塞窗口与通告窗口中的最小值作为发送上限。拥塞窗口是发送方使用的流量控制，而通告窗口则是接收方使用的流量控制。发送方开始时发送一个报文段，然后等待 ACK。当收到该ACK时，拥塞窗口从1增加为2，即可以发送两个报文段。当收到这两个报文段的 A C K时，拥塞窗口就增加为4。这是一种指数增加的关系。<br /><br />

拥塞避免算法<br /><br />

　　网络中拥塞的发生会导致数据分组丢失，需要尽量避免。在实际中，拥塞算法与慢启动通常在一起实现，其基本过程： <br /><br />
　　 1. 对一个给定的连接，初始化cwnd为1个报文段，ssthresh为65535个字节。 <br /><br />
　　 2. TCP输出例程的输出不能超过cwnd和接收方通告窗口的大小。拥塞避免是发送方使用 的流量控制，而通告窗口则是接收方进行的流量控制。前者是发送方感受到的网络拥塞的估 计，而后者则与接收方在该连接上的可用缓存大小有关。 <br /><br />
　　 3. 当拥塞发生时（超时或收到重复确认），ssthresh被设置为当前窗口大小的一半（cwnd 和接收方通告窗口大小的最小值，但最少为2个报文段）。此外，如果是超时引起了拥塞，则 cwnd被设置为1个报文段（这就是慢启动）。 <br /><br />
　　 4. 当新的数据被对方确认时，就增加cwnd，但增加的方法依赖于是否正在进行慢启动或拥塞避免。如果cwnd小于或等于ssthresh，则正 在进行慢启动，否则正在进行拥塞避免。慢启动一直持续到回到当拥塞发生时所处位置的半时候才停止（因为记录了在步骤2 中制造麻烦的窗口大小的一半），然后转为执行拥塞避免。 <br /><br />
　　 慢启动算法初始设置cwnd为1个报文段，此后每收到一个确认就加 1。那样，这会使窗口按指数方式增长：发送 1个报文段，然后是2个，接着是4个……。<br /><br />
<hr />

# TCP的坚持定时器

坚持定时器的原理是简单的，当TCP服务器收到了客户端的0滑动窗口报文的时候，就启动一个定时器来计时，并在定时器溢出的时候向向客户端查询窗口是否已经增大，如果得到非零的窗口就重新开始发送数据，如果得到0窗口就再开一个新的定时器准备下一次查询。通过观察可以得知，TCP的坚持定时器使用1，2，4，8，16……64秒这样的普通指数退避序列来作为每一次的溢出时间。<br /><br />

糊涂窗口综合症<br /><br />

TCP的窗口协议，会引起一种通常叫做糊涂窗口综合症的问题，具体表现为，当客户端通告一个小的非零窗口时，服务器立刻发送小数据给客户端并充满其缓冲区，一来二去就会让网络中充满小TCP数据报，从而影响网络利用率。对于发送方和接收端的这种糊涂行为。TCP给出了一些建议（或者是规定）。<br /><br />

接收方不通告小窗口。通常的算法是接收方不通告一个比当前窗口大的窗口（可以为0），
除非窗口可以增加一个报文段大小（也就是将要接收的MSS）或者可以增加接收方缓存空间
的一半，不论实际有多少。<br /><br />
发送方避免出现糊涂窗口综合症的措施是只有以下条件之一满足时才发送数据： ( a )可
以发送一个满长度的报文段； ( b )可以发送至少是接收方通告窗口大小一半的报文段； ( c )可以
发送任何数据并且不希望接收ACK（也就是说，我们没有还未被确认的数据）或者该连接上
不能使用Nagle算法。<br /><br />
ok，现在我们回忆一下，可以发现TCP的很多规定都是为了在一次传送中发送尽量多的数据，例如捎带ACK数据报文的策略，Nagle算法，重传时发送包含原数据报文的策略，等等。<br /><br />
<hr />

# TCP的保活定时器

保活定时器更加的简单，还记得FTP或者Http服务器都有Sesstion Time机制么？因为TCP是面向连接的，所以就会出现只连接不传送数据的“半开放连接”，服务器当然要检测到这种连接并且在某些情况下释放这种连接，这就是保活定时器的作用。其时限根据服务器的实现不同而不通。另外要提到的是，当其中一端如果崩溃并重新启动的情况下，如果收到该端“前生”的保活探察，则要发送一个RST数据报文帮助另一端结束连接。

<hr />