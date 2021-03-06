## 概述

 很多不同的厂家生产各种型号的计算机，它们运行完全不同的操作系统，但TCP/IP协议族允许它们相互进行通信。 

#### **1.分层**

TCP/IP不是一个协议，而是一个协议族，通常它被认为是一个四层的协议系统，下面展示了TCP/IP协议族中不同层次的协议

<img src=".\img_tcpip\1_1.png" style="zoom: 50%;" />

#### **2.互联网的地址**

互联网上每个接口必须有一个唯一的Internet地址（IP地址）。IP地址（IPv4）长32bit，下面是5类不同的互联网地址格式

<img src=".\img_tcpip\1_2.png" style="zoom:50%;" />

 下面是各类IP地址的范围 

<img src=".\img_tcpip\1_3.png" style="zoom:67%;" />

#### **3.端口号**

TCP和UDP采用16bit的端口号来识别应用程序

#### **4.RFC**

RFC(Request For Comments)-意即“请求评议”，包含了关于Internet的几乎所有重要的文字资料。可以看做是一个标准文档。

#### **5.应用编程接口**

使用TCP/IP协议的应用程序通常采用两种应用程序接口（API）:socket和TLI（运输层接口 Transport Layer Interface）

## 链路层

在TCP/IP协议族中，链路层主要有三个目的：

1.为IP模块发送和接收IP数据报

2.为ARP模块发送ARP请求和接收ARP应答

3.为RARP请求和接收RARP应答

TCP/IP支持多种不同的链路层协议，本文主要讨论以太网链路层协议、PPP协议以及大多数实现都包含的环回（loopback）驱动程序。

 使用ifconfig命令查看本机的网络接口 

![](.\img_tcpip\1_4.png)

 其中eth0就是以太网接口，lo是环回接口。 

####  **1.以太网IP数据包的封装** 

<img src=".\img_tcpip\1_5.png" style="zoom: 50%;" />

 链路层使用了48bit的MAC地址来标识一台主机，该地址是唯一的。 

#### **2.PPP协议（点对点协议）**

 

 

#### **3.环回接口**

环回接口允许在同一台主机上的客户程序和服务器程序通过TCP/IP通信。

A类网络号127就是为环回接口预留的。根据惯例，大多数系统把IP地址127.0.0.1分配给这个接口，并命名为localhost。

 

 

#### **4.最大传输单元MTU**

ifconfig命令的输出中有一项代表数据帧的长度限制的数据：MTU。代表的是数据帧的最大传输单元。

如果IP层有一个数据报要传，而且数据的长度比链路层的MTU还大，那么IP层就需要进行分片，使得每一片都小于MTU。

## 网际协议

IP是TCP/IP协议族中最为核心的协议。所有的TCP、UDP、ICMP及IGMP数据都以IP数据报格式传输。

IP提供不可靠、无连接的数据报传送服务。

1.不可靠：就是它不能保证IP数据报能成功地到达目的地。IP仅提供最好的传输服务。

2.无连接：每个数据报的处理都是相互独立的，IP数据报可以不按发送顺序接收。

####  **1.IP首部** 

<img src=".\img_tcpip\3_1.png" style="zoom: 50%;" />

其中TTL（time-to-live）生存时间字段设置了数据报可以经过的最多路由器数。

一旦经过一个处理它的路由器，它的值就减去1。当该字段的值为0时，数据报就被丢弃。并发送ICMP报文通知源主机

#### **2.IP路由选择**

IP路由选择是简单的，大多数主机都是采用下面的简单机制。

如果目的主机与源主机直接相连（点对点链路）或在一个共享网络上（以太网），那么IP数据报就直接送到目的主机上。

否则，主机把数据报发往一默认路由器上，由路由器来转发数据报。

 

在一般的体制中，IP层在内存中由一个路由表。当收到一份数据报并进行发送时，它都要对该表搜索一次。

###### **路由表中的每一项包含下面这些信息：**

1.目的地址。既可是一个完整的主机地址，也可以是一个网络地址，由该表的标志字段来指定。

2.下一跳路由器的IP地址，或者有直接连接的网络IP地址。

3.标志

4.为数据报传输指定一个网络接口

###### **IP路由选择主要完成以下的功能：**

1.搜索路由表，寻找能与目的IP地址完全匹配的表目。如果找到，则把报文发送给该表目指定的下一跳路由器或直接连接的网路接口。

2.搜索路由表，寻找能与目的网络号相匹配的表目。如果找到，则把报文发送给该表目指定的下一跳路由器或直接连接的网络接口。

3.搜索路由器，寻找标为默认的条目。如果找到，则把报文发送给该表目指定的下一跳路由器。

如果上面这些步骤都没有成功，那么该数据报就不能被传送。

#### **3.子网寻址**

现在所有的主机都要求支持子网编址（把主机号再分成一个子网号和主机号）

在InterNIC获得某类IP网络号后，就由当地的系统管理员来进行分配，由他来决定是否建立子网，以及分配多少比特给子网号和主机号。

下面是一个B类地址的一种子网编址

<img src=".\img_tcpip\3_2.png" style="zoom:75%;" />

#### **4.子网掩码**

除了IP地址以外，主机还需要知道有多少比特用于子网号及多少比特用于主机号，这是通过子网掩码来确定的。

这个掩码是一个32bit的值，其中值为1的比特留给网络号和子网号，为0的比特留给主机号。下图是一个B类地址的两种不同的子网掩码格式。

<img src=".\img_tcpip\3_3.png" style="zoom:75%;" />

给定IP地址和子网掩码后，主机就可以确定IP数据报的目的是：

1.本子网上的主机

2.本网络中的其他子网中的主机

3.其他网络上的主机

## ARP/RARP

数据链路如 以太网都有自己的寻址机制（MAC）地址，而IP层使用的是IP地址。

当一台主机把以太网数据发送定位于同一局域网上的另一台主机时，是根据MAC地址来确定目的接口的。设备驱动程序从不检查IP数据报中的目的IP地址。

地址解析为这两种不同的地址形式提供映射。

<img src=".\img_tcpip\4_1.png" style="zoom:75%;" />

**一个例子**

当我们键入下面这个形式的命令：

<img src=".\img_tcpip\4_2.png" style="zoom:75%;" />

 都会进行以下的步骤： 

<img src=".\img_tcpip\4_3.png" style="zoom: 50%;" />

(1) 应用程序FTP客户端调用函数gethostbyname(3)把主机名(bsdi)转换成32bit的IP地址

(2) FTP客户端请求TCP用得到的IP地址建立连接

(3) TCP发送一个连接请求分段到远端的主机，即用上述IP地址发送一份IP数据报

(4) 如果目的主机在本地网络上，那么IP数据报可以直接送到目的主机上。如果目的主机在远程网络上，则通过IP选路函数来确定吓一跳路由器地址，并让它转发IP数据报

(5) 如果是以太网，那么发送端主机必须把32bit的IP地址变换成48bit的一台网地址

(6) ARP发送一份称作ARP请求的以太网数据帧（包含目的主机的IP地址）给以太网上的每个主机。这个过程称作广播（图上虚线所示）

(7) 目的主机的ARP层收到这份广播报文后，发送一个ARP应答。这个ARP应答包含IP地址及对应的硬件地址

(8) 收到ARP应答后，使ARP进行请求--应答交换的IP数据报限制就可以传送了

(9) 发送IP数据报到目的主机

#### **ARP高速缓存**

每台主机上都有一个ARP高速缓存。这个高速缓存存放了最近Internet地址到硬件地址之间的映射记录。

高速缓存中每一项的生存时间一般为20分钟，起始时间从被创建时开始算起。

可以使用arp(8)命令来检查ARP高速缓存

![](.\img_tcpip\4_4.png)

#### **RARP：逆地址解析协议**

RARP协议是许多无盘系统在引导时用来获取IP地址的。

RARP分组格式基本上与ARP分组一致。一个RARP请求在网络上进行广播，它在分组中标明发送端的硬件地址，以请求相应的IP地址的相应。应答通常是单播传送的。

## ICMP：Internet控制报文协议

ICMP是（Internet Control Message Protocol）Internet控制报文协议。

用于在IP主机、路由器之间传递控制消息。控制消息是指网络通不通、主机是否可达、路由是否可用等网络本身的消息。

 

ICMP报文通常被IP层或更高层协议使用。ICMP报文是IP数据报内部被传输的

<img src=".\img_tcpip\5_1.png" style="zoom:50%;" />

 ICMP报文的格式如下图所示 

<img src=".\img_tcpip\5_2.png" style="zoom:50%;" />

**ICMP报文的类型**

各种类型的ICMP报文如下图所示，不同类型由报文中的类型字段和代码字段来共同决定。

<img src=".\img_tcpip\5_3.png" style="zoom:50%;" />

图中最后两列标明ICMP报文是一份查询报文还是一份差错报文。

查询报文一般有以下几种用途：

1.ICMP地址掩码请求和应答，用于无盘系统在引导过程中获取自己的子网掩码。

2.ICMP时间戳请求和应答，用于系统向另一个系统查询当前的时间。

3.使用ping命令

## Ping、Traceroute程序

#### **Ping程序**

Ping程序由Mike Muuss编写，目的是为了测试另一台主机是否可达。

该程序发送一份ICMP回显请求报文给主机，并等待返回ICMP回显应答。

<img src=".\img_tcpip\6_1.png" style="zoom:50%;" />

ping程序还能测出到这台主机的往返时间

 

#### **Traceroute程序**

Traceroute程序是一个能更深入探索TCP/IP协议的方便可用的工具

Traceroute程序可以让我们看到IP数据报从一台主机传到另一台主机所经过的路由。

 

该程序使用ICMP报文和IP首部中的TTL字段。Traceroute程序的操作过程大概如下：

先发送一份TTL字段为1的IP数据报给目的主机，处理这份数据报的第一个路由器将TTL值减1，丢弃该数据报，并发回一份超时ICMP报文，这样就得到了该路径中的第一个路由器的地址。

然后发送一份TTL值为2的数据报，继续这个过程直至该数据报到达目的主机。

## UDP：用户数据报协议

UDP是一个简单的面向数据报的运输层协议。

UDP不提供可靠性：它把应用程序传给IP层的数据发送出去，但是并不保证它们能到达目的地。

UDP首部的个字段如下图所示

<img src=".\img_tcpip\7_1.png" style="zoom:50%;" />

## 广播和多播

广播和多播仅应用于UDP。

广播指的是一个主机向网上的所有其他主机发送帧，而 多播仅发送给属于多播组的多个主机。

 

为了弄清广播和多播，需要了解主机对由信道传送过来帧的过滤过程

<img src=".\img_tcpip\8_1.png" style="zoom:50%;" />

1.首先，网卡查看由信道传送过来的帧，确定是否接收该帧，若接收后将它传往设备驱动程序。通常网卡仅接受那些目的地址为网卡物理地址或广播地址的帧。

 大多数的网卡经过配置都能接收到目的地址为多播地址或某些子网多播地址的帧。

 对于以太网，当地址中最高字节的最低位设置为1时表示该地址是一个多播地址，用十六进制可表示为01:00:00:00:00:00:00（以太网广播地址ff:ff:ff:ff:ff:ff:ff可看做是以太网多播地址的特例）。

2.如果网卡收到一个帧，这个帧将传送给设备驱动程序（如果帧检验和错，网卡将丢弃该帧），然后根据以太网首部中的类型字段对该帧进行分用（IP,ARP,RARP），传给下一层。

3.如果帧类型为IP数据报时，就传往IP层。IP层根据IP地址中的源地址和目的地址进行更多的过滤检查。如果正常，则根据IP首部的协议字段进行分用（TCP,UDP,ICMP,IGMP），传给下一层。

4.如果协议类型为UDP，则根据目的端口号进行数据报过滤。如果当前没有进程使用该目的端口号，就丢弃该数据报并产生一个ICMP不可达报文（TCP类似）。如果存在检验和错误，将被丢弃。

<img src=".\img_tcpip\8_2.png" style="zoom:50%;" />

**广播**

广播地址

host ID 为全1 的IP 地址为广播地址。例如，对于10.1.1.0 （255.255.255.0 ）网段，其广播地址为10.1.1.255 ，当发出一个目的地址为10.1.1.255 的分组时，它将被分发给该网段上的所有计算机。

 

**多播**

多播组地址

D类IP地址就是多播组地址，它的格式如下

<img src=".\img_tcpip\8_3.png" style="zoom:50%;" />

可见，多播组地址的范围为224.0.0.0到239.255.255.255

一些多播组地址被IANA确定为知名地址。例如：224.0.0.1代表“该子网的所有系统组”，244.0.0.2代表“该子网的所有路由器组”。

## IGMP：Internet组管理协议

本章将介绍用于支持主机和路由器进行多播的Internet组管理协议（IGMP）

它让一个物理网络上的所有系统知道主机当前所在的多播组。多播路由器需要这些信息以便知道多播数据报应该向那些接口转发。

跟ICMP一样，IGMP也被当做IP层的一部分，IGMP报文通过IP数据报进行传输。

<img src=".\img_tcpip\9_1.png" style="zoom:50%;" />

####  **IGMP报文** 

<img src=".\img_tcpip\9_2.png" style="zoom:50%;" />

 IGMP类型为1说明是由多播路由器发送的查询报文，为2说明是主机发送的报告报文。 

#### **IGMP协议**

加入一个多播组

多播的基础就是一个进程的概念，该进程在一个主机的给定接口上加入了一个多播组。

 

IGMP报告和查询

多播路由器使用IGMP报文来记录与该路由器相连网络中组成员的变化情况。

1.当第一个进程加入一个组时，主机就发送一个IGMP报告。

2.进程离开一个组时，主机不发送IGMP报告。主机知道在确定的组中不再有组成员后，在随后收到的IGMP查询中就不再发送报告报文。

3.多播路由器定时发送IGMP查询了解是否还有任何主机包含在属于多播组的进程。多播路由器必须向每个接口发送一个IGMP查询。

4.主机通过发送IGMP报告来响应一个IGMP查询，对每个至少还包含一个进程的组均要发回IGMP报告。

下图显示了两个IGMP报文，一个是主机发送的报告，另一个是路由器发送的查询。

<img src=".\img_tcpip\9_3.png" style="zoom:50%;" />

## DNS:域名系统

域名系统（DNS Domain Name System）是一种用于TCP/IP应用程序的分布式数据库，它提供主机名字和IP地址之间的转换及有关电子邮件的选路信息。

 

从应用角度上看，对DNS的访问时通过一个地址解析器（resolver）来完成的。

在Unix主机中，该解析器主要是通过两个库函数gethostbyname(3)和gethostbyaddr(3)来访问的。前者接收主机名字返回IP地址，而后者接收IP地址来寻找主机名字。

解析器通过一个或多个名字服务器来完成这种相互转换。

#### **DNS基础**

DNS的名字空间具有层次结构，下图显示了这种层次结构

<img src=".\img_tcpip\10_1.png" style="zoom:50%;" />

 应用程序通过名字解析器将主机名跟IP地址进行转换。名字解析器将向一个本地名字服务器发送查询请求，这个名字服务器可能通过某个根名字服务器或其他名字服务器来完成这个查询。

## TCP:传输控制协议

 与UDP协议相比，TCP提供一种面向连接的、可靠的字节流服务。 

#### **TCP首部**

跟UDP一样，TCP数据被封装在一个IP数据报中，下面显示TCP的首部数据格式

<img src=".\img_tcpip\11_1.png" style="zoom: 67%;" />

每个TCP段都包含源端和目的端的端口号，用于寻找发送端和接收端应用程序。

序号用来标识从TCP发送端向TCP接收端发送的数据字节流，确认序号包含发送确认的一段所期望收到的下一个序号。

在TCP首部中由6个标志比特。它们中的多个可同时设置为1，下面简单介绍一下它们的用法：

URG   紧急指针有效

ACK   确认序号有效

PSH   接收方应该尽快将这个报文段交给应用层

RST   重建连接

SYN   同步序号用来发起一个连接

FIN   发送端完成发送任务

TCP的流量控制由连接的每一端通过声明的窗口大小来提供。

 

后面章节将由TCP的更详细的介绍

## TCP连接的建立和终止

**建立连接**

建立一个TCP连接时会发生下述情况

![](.\img_tcpip\12_1.png)

 

1.客户TCP发送一个SYN（同步）分节，它告诉服务器将在（待建立）连接中发送的数据的初始序列号。

2.服务器确认（ACK）客户的SYN，同时自己也得发送一个SYN分节，它含有服务器将在同一连接中发送数据的初始序列号。

3.客户确认服务器的SYN。

 

 

**终止连接**

TCP终止一个连接需要4个分节

<img src=".\img_tcpip\12_2.png" style="zoom:75%;" />

1.某个应用进程首先调用close，我们称该端执行主动关闭。该端的TCP于是发送一个FIN分节，表示数据发送完毕。

2.接受到这个FIN的对端执行被动关闭。这个FIN由TCP确认。它的接收也作为一个文件结束符传递给接收端应用程序。

3.一段时间后，接收到这个文件结束符的应用进程将调用close关闭它的套接字。这导致它的TCP也发送一个FIN。

4.接收这个最终FIN的原发送端TCP确认这个FIN。

 

 

**连接建立的超时**

有很多情况导致无法建立连接，一种情况是服务器主机没有处于正常状态。

TCP连接超时会进行SYN重传，第2个SYN与第1个的间隔是5.8秒，而第3个与第2个的间隔是24秒。大多数伯克利系统将建立一个新连接的最长时间限制为75秒。

 

 

**TCP的半关闭**

TCP提供了连接的一端在结束它的发送后还能接收来自另一端数据的能力。

<img src=".\img_tcpip\12_3.png" style="zoom:50%;" />

## TCP的交互数据流

TCP需要同时处理两类数据：块数据、交互数据。

本章将以Rlogin应用为例观察交互数据的传输过程。

 

 

**交互式输入**

首先观察在一个Rlogin连接上键入一个交互命令时所产生的数据流（每键入一个交互按键都会产生一个数据分组），这会产生4个报文段：

<img src=".\img_tcpip\13-1.png" style="zoom:75%;" />

1.来自客户的交互按键；

2.来自服务器的按键确认；

3.来自服务器的按键回显；

4.来自客户的按键回显确认。

然而，我们一般可以将报文段2和报文段3进行合并。

下图是在一个Rlogin连接中键入data\n（加上回车换行）时的 tcpdump输出结果

<img src=".\img_tcpip\13-2.png" style="zoom:75%;" />

 

 

**经受时延的确认**

通常TCP在接收数据时并不立即发送ACK，它会推迟发送，以便将ACK与需要沿该方向发送的数据一起发送。

绝大多数实现采用的时延为200ms，也就是说，TCP将以最大200ms的时延等待是否有数据一起发送。

![](.\img_tcpip\13-3.png)

## TCP的成块数据流

本章将介绍TCP所使用的被称为滑动窗口协议的一种流量控制方法。

该协议允许发送方在停止并等待确认前可以连续发送多个分组，这样就可以加速数据的传输。

 

 

**滑动窗口**

下图用可视化的方法显示了滑动窗口协议

<img src=".\img_tcpip\14-1.png" style="zoom:75%;" />

我们使用三个术语来描述窗口左右边缘的运动：

1.称窗口左边沿右边缘靠近为窗口合拢。这种现象发生在数据被发送和确认时。

2.当窗口右边沿右移动将允许发送更多的数据，我们称之为窗口张开。

3.当右边沿向左移动时，我们称之为窗口收缩。

![](.\img_tcpip\14-2.png)