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

7. 代理     
网络通信过程中可能存在代理服务器。
`代理服务器`：基本行为就是接收客户端发送的请求后转发给其他服务器，代理服务器不改变请求URI并直接发送请求到前方持有资源的服务器。     
`源服务器`：持有资源实体的服务器被称为源服务器。从源服务器返回的响应经过代理服务器后再传给客户端。      
使用代理服务器可以缓存技术减少网络宽带，针对特定网站进行访问控制等。        
代理服务器主要分为`缓存代理`和`透明代理`。缓存带理会预先将资源副本缓存到代理服务器上便于后续访问，透明代理转发请求或响应时，不对报文做任何加工。反之为非透明代理。

8. 网关和隧道       
`网关`：网关的工作机制和代理类似，不同的是网关可以将HTTP请求转化为其他协议进行通信。利用网关可以提高通信安全性。        
`隧道`：隧道可按要求建立起一条与其他服务器的通信线路，隧道本身不会去解析HTTP协议。目的是确保客户端能与服务器进行安全的通信。


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

状态码的作用是在客户端向服务端发送请求时，描述返回的请求结果。通过状态码用户可以知道客户端的请求是否成功以及相关信息。      
状态码由三位数字和描述短语组成，如：`200 OK`        

| 状态码 | 类别 | 说明 |    
| --- | --- | --- |     
| 1XX | Informational（信息性状态码） | 表示接收的请求正在处理 |      
| 2XX | Success（成功状态码） | 请求正常处理完毕。如：200 OK |      
| 3XX | Redirection（重定向状态码） | 表示需要进行附加操作以完成请求 |      
| 4XX | Client Error（客户端错误状态码） | 表示服务器无法处理该请求。如：404 Not Found |      
| 5XX | Server Error（服务端错误状态码） | 表示服务器处理请求出错。如：500 Internal Server Error |      

### 什么是HTTPS

#### HTTP安全缺点

1. 通信使用明文（不加密），报文内容可能会被窃听     
HTTP本身不具备加密功能，无法对通信整体进行加密，TCP/IP通信时可能会被窃听，导致HTTP通信内容无法保证安全性。  
解决：      
    * 使用SSL（安全套接层）或TSL（安全层传输协议）的组合使用来加密HTTP通信内容。
    * 将HTTP报文主体内容加密。但是加密后的内容也可能被篡改。

2. 不验证通信方的身份，可能遭遇伪装的报文           
HTTP在请求和响应时不会对通信方的身份进行确认。      
任何人都可以发送请求，导致无法判断是否目标客户端以及产生DoS攻击等问题。     
HTTP内容可能会被拦截篡改，导致客户端收到的假服务器的信息。

3. 无法证明报文的完整性，报文可能遭篡改

#### HTTPS

`HTTPS = HTTP + 加密 + 认证 + 完整性保护`       
HTTP将上述的缺点改正后，做到通信内容加密处理、通信双方认证、通信内容完整性保护，那么就是HTTPS。     

HTTPS并不是应用层的新协议，只是将HTTP通信接口用SSL和TSL协议代替。通常HTTP直接和TCP通信，HTTPS变为了HTTP-->SSL-->TCP。
即在HTTP与TCP通信之间加了一层SSL协议。      
在采用SSL后，HTTP 就拥有了 HTTPS 的加密、证书和完整性保护这些功能。     

加密方式:       
* 共享密钥加密/对称加密，加解密使用同一个密钥，转交密钥的过程中存在风险。
* 公开密钥加密/非对称加密，使用公开密钥加密，私有密钥解密，处理速度慢，但是具有较高的安全性。       

HTTPS采用混合加密机制。在交换密钥环节使用公开密钥加密方式，之后的建立通信交换报文阶段则使用共享密钥加密方式。       
通信时使用共享密钥效率比公开密钥高。        

由于HTTPS采用SSL导致比HTTP慢2到100倍。主要由于客户端服务端加解密耗费CPU等资源和进行SSL通信导致网络负载慢。

### HTTP现状和websocket

#### HTTP通信瓶颈       

日常使用中web网站会有频繁且大量的数据更新，页面需要实时快速的内容更新。这个时候可能就需要频繁的发送HTTP请求，造成资源浪费。
并且由于以下HTTP的特性导致了发送瓶颈。 
* 一条连接只能发送一条请求。        
* 客户端不能接收除了请求服务端返回的响应之外的响应（服务端无法推送消息到客户端）。      
* 请求/响应首部未经压缩就发送。首部信息越多延迟越大。       
* 频繁的请求会发送相同的首部，造成浪费。        
* 可任意选择数据压缩格式。非强制压缩发送。      

#### WebSocket      

WebSocket：Web浏览器与Web服务器之间的全双工通信标准。       
Web服务器与客户端之间建立起WebSocket协议的通信连接之后可以互相发送JSON、XML、HTML或图片等任意格式的数据。      
由于WebSocket是建立在HTTP基础上的协议，连接的发起方仍旧是客户端，但是只要连接成功，双方就可以互发消息。     
WebSocket通信使用到HTTP的Upgrade首部字段，告知服务器通信协议发生改变，以达到握手的目的。
服务端返回状态码101 Switching Protocols的响应，表示握手成功。之后通信协议将从HTTP切换为WebSocket。      

![WEBSOCKET](/images/websocket.png)