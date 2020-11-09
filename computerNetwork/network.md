# 计算机网络

## TCP/IP

### 基础概念
1. 协议：进行交互的双方所遵守的规定。

2. TCP/IP协议：互联网相关的各类协议族的总称，如：UDP、FTP、HTTP、TCP等。         
![TCP/IP](/images/tcpip.png)

3. IP协议       
`IP`和`IP地址`不是同一个概念。         
IP地址是一个网络节点被分配到的地址，如：http://127.0.0.1      
IP（网络协议）是位于网络层，负责传输网络数据包的一种协议，几乎所有的网络协议都会用到IP协议（IP靠近底层，并且负责网络传输，难以绕过）。
IP使用IP地址和MAC地址来保证数据确实的传送到对方，
网络数据在多个网络设备中转的过程中使用ARP协议，根据IP地址来反查MAC地址，然后凭借MAC地址进行通信。

4. TCP协议      
TCP协议位于传输层，提供`可靠`的字节流服务。     
TCP协议将大的数据分割为以`报文段`为单位的数据包，通过`三次握手`策略来保证数据能够准确无误的发送给对方。
如果握手过程出现异常，TCP协议会再次重新发送数据。       
SYN（同步）ACK（确认）      
![TCP/IP](/images/three_hands.png)

5. DNS服务      
DNS：域名解析服务，位于应用层。     
提供域名到IP地址之间的解析服务或IP地址反查域名。

6. URI和URL     
URI：统一资源标识符。简单说URI是某个协议方案表示的资源的定位标识符。        
URL：统一资源定位符。URL是URI的一种，目前的网址就是使用URL来表示的。

---
### 协议分层
协议分层的好处：可以解耦，明确每一层的功能划分，便于后续协议维护以及变更。
1. 应用层   
简单理解应用层是最贴近用户的一层，为用户提供了可以用来进行网络传输的应用。应用层决定了向用户提供服务时的通信活动。      
如：FTP(文件传输协议)和DNS(域名系统)、http协议等。
2. 传输层       
简单说传输层负责两台计算机之间的数据传输。传输层针对应用层，提供了处于网络连接中两台计算机之间的数据传输。      
传输层有两个性质不同的协议：TCP(传输控制协议)和UDP(用户数据报协议)。
3. 网络层       
简单说网络层（网络互连层）决定了通过怎样的路径将网络数据包传输给对方计算机。      
数据包是网络传输的最小数据单位，网络层就是用来处理网络上流动的数据包，并决定将数据包传递给对方计算机的传输路线（网络上可能存在多台计算机/网络设备，一个数据从一个计算机传输到另一个计算机需要一个具体的路线，网络层决定这个路线）。     
如：IP（网络协议）
4. 数据链路层
前三层都是软件层面，数据链路层处于硬件部分，用来处理连接网络的硬件部分，如：控制操作系统、硬件的设备驱动、NIC（网络适配器（网卡））以及光纤等物理可见部分。

![TCP/IP](/images/tcpip_tran.png)       

---
## HTTP

### 什么是HTTP      

HTTP协议通过`请求`和`响应`的方式来实现客户端和服务端通信。请求访问文本或图像等资源的一端称为`客户端`，而提供资源响应的一端称为`服务端`。

#### 要点
1. HTTP是无状态协议     
HTTP不对请求和响应之间的通信状态进行保存，即每次有新的请求时便会有新的响应产生，协议不保留之前所有的请求或响应的报文信息。      
这一设计有助于协议更快的处理大量事务，确保协议可伸缩性，但是也导致了某些业务的处理变得麻烦，如登录状态的管理。

2. HTTP通过URI定位资源      
HTTP使用URI定位网络上的资源。客户端向某个资源发送请求时，将该资源的URI放入请求报文中来发送请求报文。

3. 主要方法       

| 方法 | 说明 | 支持的HTTP版本 |   
| --- | --- | --- |       
|GET | 获取URI资源，如果是文本直接返回，如果是接口则返回结果。也可传输实体主体（传参，明文传输存在安全问题） | 1.0、1.1|   
|POST | 传输实体主体（传参），也可以获取URI响应内容 |  1.0、1.1|       
|PUT | 传输文件，将文件存于报文主体中传输，没有验证机制存在安全性问题 | 1.0、1.1|          
|HEAD | 获取响应报文首部，同GET方法只是不返回报文主体部分 | 1.0、1.1|        
|DELETE | 删除文件，请求URI删除指定资源，没有验证机制存在安全问题 | 1.0、1.1|           
|OPTIONS | 查询该URI所指定的资源支持的方法（如：GET、POST等） | 1.1|        
|TRACE | 路径追踪，可以将Web服务器的请求路径返回给客户端，确认连接过程中的系列操作，容易引发跨站追踪攻击 | 1.1|      
|CONNECT | 要求使用隧道协议连接服务器，用隧道协议进行TCP通信，主要使用SSL和TSL协议把通信内容加密后通过隧道传输 | 1.1|        
|LINK | HTTP1.1已废弃 | 1.0|       
|UNLINK | HTTP1.1已废弃 | 1.0|      



4. HTTP持久连接

由于HTTP协议初始版本中每进行一次HTTP通信就要断开一次TCP连接，如果请求的网页中包含多个图片资源等就会产生大量的HTTP请求，导致频繁建立与断开TCP连接，增加通信量开销，所以HTTP1.1和部分1.0增加了`持久连接`功能。        
持久连接特点：任意一端没有明确提出断开连接则双方保持TCP连接状态。       
优点：减少了TCP重复连接和断开的网络开销，减轻服务的负载，提高HTTP响应速度，使HTTP可以同时并发多个请求（多请求时明显减少响应时间）。

5. Cookie

由于HTTP是无状态协议，所以会导致网站难以管理用户登录状态等问题，问了解决这个问题引入了`Cookie技术`。      
Cookie技术通过在请求和响应报文中写入Cookie来控制客户端状态。        
过程：客户端向服务端发送请求--> 服务端在响应报文中通过首部字段Set-Cookie，通知客户端保存Cookie信息--> 客户端再次发送请求时会在请求报文中自动加入Cookie值--> 服务端收到客户都的Cookie值对比服务器记录来确认客户端状态


### HTTP通信过程简述

1. 客户端想要向某个网址发送请求（如：http://www.baidu.com）--> DNS将网址解析为对应的IP地址返回给客户端（如：http://127.0.0.1）。
2. 客户端发送：HTTP协议生产针对目标网站的报文--> TCP协议将HTTP报文分割成报文段，并保证可靠的传递给对方--> IP协议搜索对方地址，将HTTP报文段经由合适的路径发送给对方。
3. 服务端接收：TCP协议接收到对方通过IP协议发送的报文段--> TCP协议将收到的报文段重新组合为完整的HTTP报文--> HTTP协议通过报文明确客户端的请求内容。

![HTTP](/images/http_1.png)     

### HTTP报文

HTTP报文分为`请求报文`和`响应报文`。报文是由多行数据构成的字符串文本，其中标识了通信过程中所需内容。        
报文组成：      
    请求行（位于请求报文中），包含`请求方法`、`URI`、`HTTP版本`三部分内容。         
    状态行（位于响应报文中），包含表示响应结果的`状态码`、`原因短语`、`HTTP版本`三部分内容。    
    首部字段（请求和响应报文中都包含），包含表示请求和响应的各种`条件`和`属性`的各类首部。首部一般有四种：`通用首部`、`请求首部`、`响应首部`、`实体首部`.       
    其他，可能包含HTTP的RFC里未定义的首部（Cookie等）。

![HTTP_MESSAGE](/images/message.png)

![HTTP_MESSAGE](/images/message_1.png)

#### 报文和实体的差别

报文：如上所述，是HTTP通信中的基本单位，负责控制HTTP通信的状态和属性控制。
实体：作为请求或响应的数据来传输，由`实体首部`和`实体主体`组成。如：post请求中携带的账号密码和响应中携带的网页数据都是实体。        

`内容编码`：为了便于实体传输，HTTP可以使用内容编码功能，将实体在服务端进行压缩传输并在客户端解码恢复。      
`分块传输编码`：在传输大容量数据时，通过把数据分割成多块，能够让浏览器逐步显示页面。        
其他：略。

### HTTP状态码

### 什么是HTTPS

### HTTP现状和websocket
